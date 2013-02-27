# Private messages

Private message is a sub-resource for [Private Conversation](Private Conversations), so it should always be accessed in that context. Currently only regular messages are supported in Private Conversations, but file messages and possibly other types will follow soon.

## Send a message
```
POST /private/:user_id/messages
```
Send a private chat message to the specified user.

### Input
* `event`
    One of the valid private message events. Determines the type of message being sent to Flowdock. See Message Types section below. Required.
* `content`
    The actual message. The format of content depends on the event. See Message Types section below. Required.

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
The event parameter in the message data defines the type of message. More at [Message Types](Message Types).

#### Normal private message
Event: `message`

For private messages, `content` is simply a string which represents the chat message's content. _Maximum length of the content is 8096 characters._

<div id="/list"></div>
## List private messages
```
GET /private/:user_id/messages
```
Lists messages from a [Private Conversation](Private Conversations), filtered by parameters.

### Parameters

* `event` &ndash; Filter messages by event type. Leave blank to get all messages, which is also the default. Multiple event types can be specified by separating them with commas.

* `limit` &ndash; Maximum number of messages to return. Defaults to 30, accepted values range from 1 to 100.

* `sort` &ndash; Descending order is used by default in fetching messages. Use parameter value `asc` for ascending order. Note: the response JSON will always have the matching messages in ascending order by their id. The sort parameter is for toggling between fetching the latest or the oldest matching messages.

* `since_id` and `until_id` &ndash; Get messages starting from or leading to a message. The message specified with the parameter won't be included in the response.

* `search` &ndash; Full text search for message content. The search matches messages that contain all the specified keywords. Separate the keywords by spaces.

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
    "to": 42,
    "id": 3816534,
    "event": "message",
    "content": "Secret message",
    "attachments": [],
    "user": 2
  },
  {
    "app": "chat",
    "event": "message",
    "tags": [],
    "uuid": "4W_LQEybVaX-gJmi",
    "to": 2,
    "id": 45590,
    "content": "For your eyes only!",
    "sent": 1317715340213,
    "attachments": [],
    "user": 42
  },
  // ... 14 more messages
]
```
* Message fields are described below in the Show message section.

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
  "to": 42,
  "id": 3816534,
  "event": "action",
  "content": "Secret message",
  "attachments": [],
  "user": 2
}
```
* `event` &ndash; The type of the message. E.g. chat message or tweet. See [Message Types](Message Types).
* `content` &ndash; Data payload. Different in different events as documented in [Message Types](Message Types).
* `tags` &ndash; List of tags as strings. Currently tagging is not enabled for private messages.
* `app` &ndash; Deprecated.
* `uuid` &ndash; Optional client-generated universal identifier for message. Used to recognize messages sent by single Flowdock instance. Can be used to render sent messages instantly and later add necessary data (id) for tagging.
* `user` &ndash; Numerical user id of user
* `to` &ndash; Numerical user id of the recipien
* `sent` &ndash; Timestamp when message was sent. Milliseconds since Jan 1st, 1970.
* `id` &ndash; Incremental id of message. Unique only in private conversation scope.
* `attachments` &ndash; List of file attachments related to this message. Example:

```javascript
[
  {
    "content_type": "text/html",
    "content_id": "4e8b0e5bdccc2_4eec800c21ac57014@ciMac.local.mail",
    "file_name": "cucumber.html",
    "file_size": 27798, // Size in bytes
    "path": "/flows/flowdock/files/ec53a7aa7a974cdc9d07c5ca395a2f05/cucumber.html"
  }
]
```
