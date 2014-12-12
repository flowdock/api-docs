# Thread actions

[Threads](threads) can specify custom actions for the thread's external resource. Actions follow the [schema.org Action format](http://schema.org/Action).
Two types of actions are supported: [ViewAction](#/view-action) and [UpdateAction](#/update-action). Actions enable you to create [bidirectional integrations with Flowdock](how-to-create-bidirectional-integrations).

<span id="/view-action"></span>
## ViewAction
[ViewActions](http://schema.org/ViewAction) are simply links that are shown in the thread's list of actions.
#### Example ViewAction in a thread message
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
[UpdateActions](http://schema.org/UpdateAction) allow for bi-directional integrations. Users can perform simple
actions securely from Flowdock without opening new browser tabs. The integration will receive a request from Flowdock
with the user's Flowdock user ID (for authentication) to perform actions on the resource. Text input from the user is currently not supported.

Examples on how to handle the requests coming from UpdateActions can be found in [How to create bidirectional integrations](how-to-create-bidirectional-integrations).

#### Example UpdateAction in a thread message
```json
{
    "@type": "UpdateAction",
    "name": "Assign to me",
    "target": {
        "@type": "EntryPoint",
        "urlTemplate": "https://my-flowdock-app-integrator.com/issues/42?assignee=me",
        "httpMethod": "POST"
    }
}
```

### UpdateAction requests made by Flowdock
Once Flowdock receives the thread message with UpdateActions, it converts the target's urlTemplate into a signed Flowdock URL. When a Flowdock user selects the action, Flowdock will perform the specified request to the resource. The action payload data is the same as in the thread data, enriched with agent data that contains information about the Flowdock user that is performing the action.

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
        "urlTemplate": "https://my-flowdock-app-integrator.com/issues/42?assignee=me",
        "httpMethod": "POST"
    }
}
```

#### Flowdock user ID and request signature
The 'FLOWDOCK-TOKEN' header is an encoded token that contains the user's Flowdock ID, expiration time and a signature of the request body. The token is encoded with [JWT](http://jwt.io) using the OAuth client secret as a signing key.

##### Example of a decoded FLOWDOCK-TOKEN header
```json
{
    "sub": "<flowdock_user_id>",
    "exp": "<expiration time (30 seconds)>",
    "signature": "<SHA256 digested request body>"
}
```

<div id="/authentication-challenge"></div>
### Authentication challenge
If the application needs the user to authenticate in order to complete the action, the application should respond to the user's action with a 401 status code and a URL where the user can perform the authentication. The user will be instructed to open this URL in his browser as the result of the failed action.

The authentication challenge must specify the authentication URL inside the 'www-authenticate' header in the following format:

```
www-authenticate = "Flowdock-Token url=https://my-flowdock-app-integrator.com/authenticate"
```
