# How to handle UpdateActions with Ruby on Rails

This document gives examples of how to securely handle UpdateActions in your application. [UpdateActions](http://schema.org/UpdateAction) allows you to have a bidirectional integration with Flowdock – in other words, in addition to getting activity notifications from your service in Flowdock, it allows users to perform actions in your application right from within Flowdock.

The basic premise is to securely authenticate the user in the target system (your application) with their Flowdock ID and then perform the actions as the user.

### An example controller for receiving the UpdateAction request from Flowdock
```ruby
  def update_action
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

The following example demonstrates how to return an authentication challenge for the user. We append the authentication URL with a claim parameter that is uniquely created for the user that tried to perform the UpdateAction.

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
