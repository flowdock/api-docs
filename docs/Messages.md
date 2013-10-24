# Messages

There are several different [Message Types](Message Types), you can upload files and set the user's status as well as send plain chat messages. Message is a sub-resource for Flow, so it should always be accessed in that context.

## Send a message

Send a chat message, set the status, comment, upload a file...

```
POST /messages
```

or

```
POST /flows/:organization/:flow/messages
```

Comments can be posted to

```
POST /flows/:organization/:flow/messages/:message_id/comments
```

[URL breakdown](rest#/url-breakdown)


### Input
* `flow`
  The `id` of the target [flow](Flows) unless specified in the URL. _Optional_
* `event`
  One of the valid Flowdock message events. Determines the type of message being sent to Flowdock. See Message Types section below. Required.
* `content`
  The actual message. The format of content depends on the event. See Message Types section below. Required.
* `tags`
List of [tags](Tags) to be added to the message. Can be either an array (JSON only) or a string with tags delimited with commas. User tags should start with '@'. Hashtags can optionally be prefixed with "#". Tags are case insensitive. These are equivalent:
  * `["@Mike", "#cool", "awesome"]`
  * `"#awesome,cool,@mike"`
* `external_user_name`
  Name which appears as the message sender. This will change message to anonymous message, like it was sent from [Push API](Chat). _Optional_
* `message_id` when sending a comment, the id of the commented message. Must not be a comment.

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
Event: `message`

For chat messages, `content` is simply a string which represents the chat message's content. Additionally, tags are parsed from the message content. _Maximum length of the content is 8096 characters._

#### Comment message
Event: `comment`

Comments can only be posted through the message specific comments url (the last type of urls above). `content` format is the same as with `message`. _Please note_ that for forwards compatibility the `content` format is different from the `content` that's returned by the [streaming](/api/streaming) or [rest api](/api/rest) for `comment` messages.

#### Status Update
Event: `status`

`content` format is the same as with `message`. Sets the status of the user.

<div id="/send/files"></div>
#### File Upload
Event: `file`

The format of `content` is different depending on the `Content-Type` of the request. When using `application/json` the binary is sent within the JSON, Base64 encoded. Example:

```
{
  "data": "iVBORw0KGgoAAAANSUhEUgAAABQAAAAaCAYAAAC3g3x9AAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJ\nbWFnZVJlYWR5ccllPAAAAElJREFUeNpiYECA/UD8n0y8nwENUGIYTkOpAhihplMNMFHbhVQ3kIVA\ncOAD/0k1kAFP+DIO3UgZTYej6XA0HY6mw9F0CAEAAQYAk/gtCSEUikYAAAAASUVORK5CYII=\n",
  "content_type": "image/png",
  "file_name": "cabinet_icon.png"
}
```

To send the file using form data, set Content-Type as `multipart/form-data` and set the value of content as the binary part. Here's a `curl` example:

```
curl -v -X POST -F "event=file" -F "content=@path/to/file.png" https://BASIC-AUTH@api.flowdock.com/flows/ORGANIZATION/FLOW/messages
```

<div id="/list"></div>
## List Messages
```
GET /flows/:organization/:flow/messages
```
[URL breakdown](rest#/url-breakdown)

Lists messages from a flow, filtered by parameters.

### Parameters

* `event` &ndash; Filter messages by event type. Leave blank to get all messages, which is also the default. Multiple event types can be specified by separating them with commas.

* `limit` &ndash; Maximum number of messages to return. Defaults to 30, accepted values range from 1 to 100.

* `sort` &ndash; Descending order is used by default in fetching messages. Use parameter value `asc` for ascending order. Note: the response JSON will always have the matching messages in ascending order by their id. The sort parameter is for toggling between fetching the latest or the oldest matching messages.

* `since_id` and `until_id` &ndash; Get messages starting from or leading to a message. The message specified with the parameter won't be included in the response.

* `tags` &ndash; Filter messages by tags. Multiple tags can be specified by separating them with commas, which results in an _AND search_ of those tags by default (see `tag_mode` parameter). When searching with user tags, you can use either the human-readable form, `@user`, or the internal form containing the user ID, `:user:12345`. See more at [Tags](Tags).

* `tag_mode` &ndash; Toggles tag filtering of messages, by default the value is `and`. Use value `or` to search for messages that contain any of the tags specified in `tags`.

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
    "app":"chat",
    "sent":1317397485508,
    "uuid":"odHapx1VWp7WTrdQ",
    "tags":[],
    "flow": "deadbeefdeadbeef",
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
    "flow": "deadbeefdeadbeef",
    "content": "Hello World",
    "sent": 1317715340213,
    "attachments": [],
    "user": "2"
  },
  // ... 28 more messages
]
```
* Message fields are described below in the Show message section.

<div id="/show"></div>
## Show Message
```
GET /flows/:organization/:flow/messages/:id
```
[URL breakdown](rest#/url-breakdown)

Retrieve a message with the specified id.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```javascript
{
  "app":"chat",
  "sent":1317397485508,
  "uuid":"odHapx1VWp7WTrdQ",
  "tags":[],
  "flow": "deadbeefdeadbeef",
  "id":3816534,
  "event":"action",
  "content":{
    "type":"add_twitter_search",
    "description":"flowdock"
  },
  "attachments": [],
  "user":"18"
}
```
* `event` &ndash; The type of the message. E.g. chat message or tweet. See [Message Types](Message Types).
* `content` &ndash; Data payload. Different in different events as documented in [Message Types](Message Types).
* `tags` &ndash; List of tags as strings. Tags containing colon characters have [special meaning](Tags) and can be discarded when working with tagging user interfaces.
* `app` &ndash; Deprecated.
* `uuid` &ndash; Optional client-generated universal identifier for message. Used to recognize messages sent by single Flowdock instance. Can be used to render sent messages instantly and later add necessary data (id) for tagging.
* `user` &ndash; Numerical user id of user (as string)
* `sent` &ndash; Timestamp when message was sent. Milliseconds since Jan 1st, 1970.
* `flow` &ndash; Flow identifier
* `id` &ndash; Incremental id of message. Unique only in flow scope.
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

<div id="/edit"></div>
## Edit Message
```
PUT /flows/:organization/:flow/messages/:id
```
[URL breakdown](rest#/url-breakdown)

Updates a message with the specified id. Note: the message editing is limited by the `event` of the message and the properties to be updated. See details from the input section below.

### Input
* `content`
    The message content. Updating content is only possible for your own messages of type `message` or `comment`.
_Optional_
* `tags`
    Full list of message [tags](Tags). Any existing tags that aren't included in this parameter are removed from the message. As in the web UI, anyone can edit the tags of any message they can see.
_Optional_

```javascript
{
  "content": "Updated content",
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


<div id="/delete"></div>
## Delete Message
```
DELETE /flows/:organization/:flow/messages/:id
```
[URL breakdown](rest#/url-breakdown)

Delete a message with the specified id. Note: only team inbox (`app=influx`) or user's own file (`event=file`) messages can be deleted, for other messages the response will be HTTP status 404 with an error message.

### Response
```
HTTP/1.1 204 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
