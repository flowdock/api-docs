# How to create bidirectional integrations

![Example bidirectional integration](/images/two-way-actions.png)

Your CA Flowdock integration can define simple user interfaces for your app inside CA Flowdock. Good examples are assign to me or resolve issue. To create these kinds of bidirectional integrations, you will first need to follow the steps in the [Integration Guide](developing-integrations). Then, once you can post activities to CA Flowdock, you will need to include an *[actions](thread-actions)* attribute in your thread data. There are two types of actions: [UpdateAction](thread-actions#/update-action) defines an HTTP request that will be sent from CA Flowdock to your application, whereas a [ViewAction](thread-actions#/view-action) acts as a simple link.

Here's an example activity message which includes an UpdateAction. This example defines an "assign to me" action. When it's clicked in the CA Flowdock UI, a signed POST request will be sent to the defined URL.


```
POST https://api.flowdock.com/messages
```
```json
{
  "flow_token": "3e2252e2e164d70ebbc5c59b9db629c8",
  "event": "activity",
  "author": {
    "name": "anttipitkanen",
    "avatar": "https://avatars.githubusercontent.com/u/946511?v=2"
  },
  "title": "Opened pull request",
  "external_thread_id": "github:component:pr:42",
  "thread": {
    "title": "Fix bug in thread API",
    "external_url": "https://github.com/flowdock/component/pull/42",
    "status": {
      "color": "green",
      "value": "open"
    },
    "actions": [
      {
        "@type": "UpdateAction",
        "name": "Assign to me",
        "target": {
          "@type": "EntryPoint",
          "urlTemplate": "https://my-flowdock-github-integrator.com/issues/42/assign-to-me",
          "httpMethod": "POST"
        }
      }
    ]
  }
}
```

## Receiving UpdateActions

The basic premise is to securely authenticate the user in the target system (your application) using their CA Flowdock user ID and then perform the actions as the user (and update the thread in CA Flowdock).

Below is an example Ruby on Rails controller action for receiving an UpdateAction request from CA Flowdock.

```ruby
  def assign_to_me
    user_id = decode_flowdock_user_id(ENV['OAUTH_APP_SECRET'])
    user = User.find_by(flowdock_user_id: user_id)
    if !user.nil?
      # Do the action here!
      render nothing: true, status: 200
    else
      response_authentication_challenge(user_id)
    end
  end
```

We use [JWT](http://jwt.io) to [sign the CA Flowdock user ID](thread-actions#/signature). The encoded token is in the 'FLOWDOCK-TOKEN' header. You need the OAuth application secret to verify the authenticity of the token. Here's an example of CA Flowdock user ID extraction and verification of the request body.

```ruby
  def decode_flowdock_user_id
    token, _ = JWT.decode(request.env['HTTP_FLOWDOCK_TOKEN'], ENV['OAUTH_APP_SECRET'])
    verify_request_signature(token["signature"])
    token["sub"].to_s
  end

  def verify_request_signature(signature)
    request_data = request.body.read
    if !Rack::Utils.secure_compare(signature, Digest::SHA256.hexdigest(request_data))
      raise Forbidden.new()
    end
  end
```

## Authentication challenge

If the action cannot be completed because the user needs to authenticate first, your app should respond with an [authentication challenge](thread-actions#/authentication-challenge). The CA Flowdock UI will then communicate the situation and present the user with the option to proceed to the link you've defined to authenticate.

The following example demonstrates how to return an authentication challenge for the user. As a best practice, we've appended the authentication URL with a claim parameter that is uniquely created for the user that tried to perform the UpdateAction. This enables one click authentication that avoids [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery).

When we receive the request for the new authentication, we make sure that the claim's CA Flowdock user ID matches with the CA Flowdock user ID that is received from user's [authorization for CA Flowdock](production-integrations#/oauth2-authorize).

```ruby
  def response_authentication_challenge(user_id)
    response.headers["www-authenticate"] = "Flowdock-Token url=#{authentication_claim(user_id)}"
    render json: '{"error": "Authentication required"}', status: 401
  end

  def authentication_claim(user_id)
    payload = { user: user_id, exp: 15.minutes.from_now.to_i }
    token = JWT.encode(payload, ENV["COOKIE_SECRET"])
    "https://my-flowdock-github-integrator.com/authorization?claim=#{token}"
  end
```

Once the user has successfully authenticated, the one-click actions should work smoothly.
