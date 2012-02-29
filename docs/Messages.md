# Messages

There are several different [Message Types](Message Types), you can upload files and set the user's status as well as send plain chat messages. Message is a sub-resource for Flow, so it should always be accessed in that context.

## Send a message
```
POST /flows/:organization/:flow/messages
```
Send a chat message, set the status, comment, upload a file...


### Input
* `event`
    One of the valid Flowdock message events. Determines the type of message being sent to Flowdock. See Message Types section below. Required.
* `content`
    The actual message. The format of content depends on the event. See Message Types section below. Required.
* `tags`
List of [tags](Tags) to be added to the message. Can be either an array (JSON only) or a string with tags delimited with commas. User tags should start with '@'. Hashtags can optionally be prefixed with "#". Tags are case insensitive. These are equivalent:
  * `["@Mike", "#cool", "awesome"]`
  * `"#awesome,cool,@mike"`

```javascript
{
  "event": "message",
  "content": "Howdy-Doo @Jackie #awesome",
  "tags":  ["todo", "#feedback", "@all"]
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

#### Normal Chat message
_Event: `message`_

For chat messages, the content is simply a string which represents the chat messages content. Additionally, tags are parsed from the message content.

#### Status update
_Event: `status`_

Format is the same as with `message`. Sets the status of the user.

<div id="/list"></div>
## List Messages
```
GET /messages
```
Lists messages from a flow, filtered by parameters.

### Parameters

* `event` &ndash; Filter messages by event type. Leave blank to get all messages, which is also the default. Multiple event types can be specified by separating them with commas.

* `limit` &ndash; Maximum number of messages

* `sort` &ndash; Descending order is used by default in fetching messages. Use parameter value `asc` for ascending order. Note: the response JSON will always have the matching messages in ascending order by their id. The sort parameter is for toggling between fetching the latest or the oldest matching messages.

* `since_id` and `until_id` &ndash; Get messages starting from or leading to a message. The message specified with the parameter won't be included in the response.

* `tags` &ndash; Filter messages by tags. Multiple tags can be specified by separating them with commas, which results in an _AND search_ of those tags. When searching with user tags, use the meta representation. See more at [Tags](Tags).

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```javascript
[
  {
    "app":"chat",
    "sent":1317397485508,
    "uuid":"odHapx1VWp7WTrdQ",
    "tags":[],
    "flow": "subdomain:sample-flow",
    "id":3816534,
    "event":"action",
    "content":{
      "type":"add_twitter_search",
      "description":"flowdock"
    },
    "attachments": [],
    "user":"18"
  },
  {
    "app": "chat",
    "event": "message",
    "tags": [],
    "uuid": "4W_LQEybVaX-gJmi",
    "id": 45590,
    "flow": "subdomain:sample-flow",
    "content": "Hello World",
    "sent": 1317715340213,
    "attachments": [],
    "user": "2"
  },
  // ... 28 more messages
]
```
* `event` &ndash; The type of the message. E.g. chat message or tweet. See [Message Types](Message Types).
* `content` &ndash; Data payload. Different in different events as documented in [Message Types](Message Types).
* `tags` &ndash; List of tags as strings. Tags containing colon characters have [special meaning](Tags) and can be discarded when working with tagging user interfaces.
* `app` &ndash; Deprecated.
* `uuid` &ndash; Optional client-generated universal identifier for message. Used to recognize messages sent by single Flowdock instance. Can be used to render sent messages instantly and later add necessary data (id) for tagging.
* `user` &ndash; Numerical user id of user (as string)
* `sent` &ndash; Timestamp when message was sent. Milliseconds since Jan 1st, 1970.
* `flow` &ndash; Identifier of flow. See [Flows](Flows).
* `id` &ndash; Incremental if of message. Unique only in flow scope.
* `attachments` &ndash; List of file attachments related to this message. Example:

```javascript
[
  {
    "content_type":"text/html",
    "content_id":"4e8b0e5bdccc2_4eec800c21ac57014@ciMac.local.mail",
    "file_name":"cucumber.html",
    "file_size":27798, // Size in bytes
    "path":"/flows/flowdock/files/ec53a7aa7a974cdc9d07c5ca395a2f05/cucumber.html"
  }
]
```
