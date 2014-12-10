# How to create bi-directional integrations (with Ruby)

To create bi-directional integrations, you will first need to follow the steps in [How to integrate](how-to-integrate). After the integration is all set and you can post activities to Flowdock, you will need to include [UpdateActions](thread-actions#/update-action) in the thread's actions attribute.

## Example activity request with an UpdateAction

```
POST https://api.flowdock.com/messages

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

## Handling UpdateActions

The basic premise is to securely authenticate the user in the target system (your application) with their Flowdock user ID and then perform the actions as the user.

### An example controller for receiving the UpdateAction request from Flowdock
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

### Example Flowdock user ID extraction and request verification
```ruby
  def decode_flowdock_user_id
    token, _ = JWT.decode(request.env['HTTP_FLOWDOCK_TOKEN'], ENV['OAUTH_APP_SECRET'])
    verify_request_not_expired(token["exp"])
    verify_request_signature(token["signature"])
    token["sub"].to_s
  end

  def verify_request_signature(signature)
    request_data = request.body.read
    if !Rack::Utils.secure_compare(signature, Digest::SHA256.hexdigest(request_data))
      raise Forbidden.new()
    end
  end

  def verify_request_not_expired(expiration_time)
    if expiration_time < Time.now.to_i
      raise Forbidden.new()
    end
  end
```

## Authentication challenge

The following example demonstrates how to return an [authentication challenge](thread-actions#/authentication-challenge) for the user. We append the authentication URL with a claim parameter that is uniquely created for the user that tried to perform the UpdateAction. This enables one click authentication that avoids [CSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery).

When we receive the request for the new authentication, we make sure that the claim's Flowdock user ID matches with the Flowdock user ID that is received from user's [authorization for Flowdock](how-to-integrate#/oauth2-authorize).

### Example authentication challenge to the Flowdock user
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
