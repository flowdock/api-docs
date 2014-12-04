# Thread actions

Threads can specify custom actions for the thread's external resource. Actions follow the [schema.org Action format](http://schema.org/Action).
Two types of actions are supported: [ViewAction](#/view-action) and [UpdateAction](#/update-action).

<span id="/view-action"></span>
## ViewAction
[ViewActions](http://schema.org/ViewAction) are simply links that are shown in the thread's action listing.
### Example thread action
```json
{
    "@type": "ViewAction",
    "url": "https://github.com/flowdock/component/pull/42/files",
    "name": "Diff",
    "description": "View diff in GitHub"
}
```

<span id="/update-action"></span>
## UpdateAction
[UpdateActions](http://schema.org/UpdateAction) enable bidirectional integrations. Users can perform simple
actions securely from Flowdock without opening new browser tabs. The integration will receive a request from Flowdock
with the user's Flowdock id so that it can utilize it as an authentication method for performing actions to the resource.

User input is currently not supported.

Examples on how to handle the requests coming from UpdateActions can be found in [How to handle UpdateActions in Ruby](how-to-thread-actions).

### Example thread action data
```json
{
    "@type": "UpdateAction",
    "name": "Assign to me",
    "target": {
        "@type": "EntryPoint",
        "urlTemplate": "https://my-flowdock-github-integrator.com/issues/42?assignee=me",
        "httpMethod": "POST"
    }
}
```

### Requests from UpdateActions
Once Flowdock receives the thread with UpdateActions, it converts the target's urlTemplate into a signed Flowdock URL.
When a Flowdock user clicks the action, Flowdock will perform the specified request to the resource. The action payload data is the same that is given in the thread data - augmented with agent data which contains some information about the Flowdock user that is processing the action.

#### Example action request from Flowdock
```json
{
    "@type": "UpdateAction",
    "name": "Assign to me",
    "agent": {
        "@type": "Person",
        "name": "John Doe",
        "image": "https://cloudfront.com/avatar/url/120"
    },
    "target": {
        "@type": "EntryPoint",
        "urlTemplate": "https://my-flowdock-github-integrator.com/issues/42?assignee=me",
        "httpMethod": "POST"
    }
}
```

#### Flowdock user id and request signature
'FLOWDOCK-TOKEN' -header is an encoded token which contains the user's Flowdock id, expiration time and a signature of the request body. The token is encoded with [JWT](http://jwt.io) using the OAuth client secret as signing key.

##### Example decoded FLOWDOCK-TOKEN header
```json
{
    "sub": "<flowdock_user_id>",
    "exp": "<expiration time (30 seconds)>",
    "signature": "<SHA256 digested request body>"
}
```

### Authentication challenge
Often the application needs the user to authenticate to complete the action. In this case the application should respond to the user's action by returning 401 with an URL where the user can create the required authentications. This URL will be then given to the user in Flowdock.

The authentication challenge must specify the authentication url by giving the URL inside the "www-authenticate" -header in the following format.
```
www-authenticate = "Flowdock-Token url=https://my-flowdock-github-integrator.com/authenticate"
```

Remember that the authentication challenge is only an URL, which will be opened by a GET request in the browser. This means that you should avoid CSRF by either
forcing the user to acknowledge the authorization creation by submitting a form by POST and validating a CSRF-token in it. Other possibility is to create a unique
authorization URL for the Flowdock user that needs authentication.
