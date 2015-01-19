# Private messages

Private message is a sub-resource for [Private conversation](private-conversations). It should always be accessed in a private conversation's context. Currently, only regular messages are supported in Private Conversations, but file messages and other message types might be supported in the future.

## Send a message
```
POST /private/:user_id/messages
```
Send a private chat message to the specified user.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| event | **Required** One of the valid private message events. Determines the type of message being sent to Flowdock. See Message Types section below.  |
| content | **Required** The actual message. The format of content depends on the event. See Message Types section below. |

```javascript
{
  "event": "message",
  "content": "Howdy-Doo @Jackie #awesome"
}
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{}
```

### Message Types
The event parameter in the message data defines the type of message. More at [Message Types](message-types).

#### Normal private message
Event: `message`

For private messages, `content` is simply a string which represents the chat message's content. The maximum length of the content is 8096 characters.

<div id="/list"></div>
## List private messages
```
GET /private/:user_id/messages
```
Lists messages from a [Private conversation](private-conversations), filtered by parameters.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| event | Filter messages by event type. Leave blank to get all messages, which is also the default. Multiple event types can be specified by separating them with commas. |
| limit | Maximum number of messages to return. Accepted values range from 1 to 100. Defaults to 30. |
| sort | Descending order is used by default. Use parameter value `asc` for ascending order. Note: the response JSON will always have the matching messages in ascending order by their `id`. The sort parameter is for toggling between fetching the latest or the oldest matching messages. |
| since\_id and until\_id | Get messages starting from or leading to a message `id`. The message specified with the parameter won't be included in the response. |
| search | Full text search for message content. The search matches messages that contain all the specified keywords. Separate the keywords by spaces. |

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```javascript
[
  {
    "app": "chat",
    "sent": 1317397485508,
    "uuid": "odHapx1VWp7WTrdQ",
    "tags": [],
    "to": "42",
    "id": 3816534,
    "event": "message",
    "content": "Secret message",
    "attachments": [],
    "user": "2",
    "created_at": "2011-09-30T15:44:45.507Z"
  },
  {
    "app": "chat",
    "event": "message",
    "tags": [],
    "uuid": "4W_LQEybVaX-gJmi",
    "to": "2",
    "id": 45590,
    "content": "For your eyes only!",
    "sent": 1317715340213,
    "attachments": [],
    "user": "42",
    "created_at": "2011-10-04T08:02:20.213Z"
  },
  // ... 14 more messages
]
```

Message fields are described in the Show private message section below.

<div id="/show"></div>
## Show private message
```
GET /private/:user_id/messages/:id
```
Retrieve a message with the specified id.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```javascript
{
  "app": "chat",
  "sent": 1317397485508,
  "uuid": "odHapx1VWp7WTrdQ",
  "tags": [],
  "to": "42",
  "id": 3816534,
  "event": "action",
  "content": "Secret message",
  "attachments": [],
  "user": "2",
  "created_at": "2011-09-30T15:44:45.507Z"
}
```

| Name          | Description  |
| ------------- | ------------ |
| event | The type of the message. E.g. chat message or tweet. See [Message Types](message-types). |
| content | Data payload. Different in different events as documented in [Message Types](message-types). |
| tags | List of tags as strings. Currently tagging is not enabled for private messages. |
| app | Deprecated. |
| uuid | Optional client-generated universal identifier for message. Used to recognize messages sent by a client. |
| user | Numerical user id of user |
| to | Numerical user id of the recipient |
| sent | Timestamp when message was sent. Milliseconds since Jan 1st, 1970. |
| id | Incremental id of message. Unique only in private conversation scope. |
| attachments | List of file attachments related to this message. Example: |

```javascript
[
  {
    "content_type": "text/html",
    "content_id": "4e8b0e5bdccc2_4eec800c21ac57014@ciMac.local.mail",
    "file_name": "cucumber.html",
    "file_size": 27798, // Size in bytes
    "path": "/private/1/2/files/ec53a7aa7a974cdc9d07c5ca395a2f05/cucumber.html"
  }
]
```
