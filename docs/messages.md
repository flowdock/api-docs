# Messages

There are several different [Message Types](message-types): in addition to sending plain chat messages, you can post content to the team inbox, upload files and set the user's status. Messages are a sub-resource of flows, meaning they should always be accessed in a flow's context.

## Send a message

Send a chat message, set a user's status, comment in a thread, upload a file, etc. You can optionally define the flow as a part of the path.

```
POST /messages
POST /flows/:organization/:flow/messages
POST /flows/:organization/:flow/threads/:id/messages
```

<div id="/post/comment"></div>
Comments (replies to a previous non-threaded message) have their own endpoint.

```
POST /comments
POST /flows/:organization/:flow/messages/:message/comments
```

Sending a message to a flow is possible using all authentication methods, including [flow tokens](authentication#source-token).

[URL breakdown](rest#/url-breakdown)


### Parameters

| Name          | Description  |
| ------------- | ------------ |
| flow | **Required when flow is not specified in the URL or when not using a flow_token.** The `id` of the target [flow](flows). Can be specified in the URL. |
| event | **Required.** The message event type. See the [Message Types](messages#/message-type) section below.  |
| content | **Required.** The message content. The format of the content depends on the event type. See the [Message Types](messages#/message-type) section below. |
| message | **Required for comments.** The `id` of the commented parent message (which must not be a comment). Can be specified in the URL. |
| tags | List of [tags](tags) to be added to the message. Can either be an array (JSON only) or a string with tags delimited with commas. User tags should start with '@'. Hashtags can optionally be prefixed with "#". Tags are case insensitive. These are equivalent: `["@Mike", "#cool", "awesome"]` and `"#awesome,cool,@mike"` |
| external\_user\_name | Name that appears as the message sender. This will change the message to an anonymous message, as if it was sent from the [Push API](chat).  |
| uuid | An optional client-generated unique string identifier. It is the client's responsibility to ensure the uniqueness (in the scope of the flow) of the uuid. This can be, for example, used to render sent messages instantly and later add necessary data (id) for tagging. |
| thread_id | The `id` of the [thread](threads) this message is being posted to. In order to post a message into a thread, this or `external_thread_id` is **required**. |
| external\_thread\_id | An ID (specified by the API user) to identify the thread this message should be added to. The ID should be unique within the [source](sources). For examples, see the integration guide's [section about posting to the inbox](production-integrations#/post-inbox). |
| thread | New state for the [thread](threads). |
| attachments | List of attachments for a message. Only valid for `activity` and `discussion` events. See [files](files). |

```json
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
Link: http://api.flowdock.com/flows/acme/main/messages/12345/comments; rel="comments"
```
```json
{
  "id": 12345,
  "event": "message",
  "content": "Howdy-Doo @Jackie #awesome",
  "tags":  ["todo", "feedback", ":user:everyone", "awesome", ":user:123"],
  "user": "2",
  "uuid":"9rOhL9RS-jhr8-YvFHRfhA",
  "sent":1385546251160,
  "app":"chat",
  "attachments":[],
  "created_at": "2013-11-27T09:57:31.160Z"
}
```

<div id="/message-type"></div>
### Message Types
The event parameter in the message data defines the type of message. More information can be found on the [Message Types](message-types) page.

#### Normal Chat message
Event: `message`

For chat messages, `content` is simply a string which represents the chat message's content. Additionally, tags are parsed from the message content. The maximum length of the content is 8096 characters.

<div id="/send/comment"></div>
#### Comment message
Event: `comment`

Comments can only be posted through the message specific comments URL. The format of `content` is the same as with `message`. Please note that for forward compatibility reasons, the format of `content` is different here than the `content` field that's returned by the [Streaming](/api/streaming) or [REST API](/api/rest) for `comment` messages.

#### Status Update
Event: `status`

The format of `content` is the same as with `message`. Sets the status of the user.

<div id="/send/files"></div>
#### File Upload
Event: `file`

The format of `content` is different depending on the `Content-Type` of the request. When using `application/json`, the binary is sent as a Base64-encoded string within the JSON. Example:

```json
{
  "data": "iVBORw0KGgoAAAANSUhEUgAAABQAAAAaCAYAAAC3g3x9AAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJ\nbWFnZVJlYWR5ccllPAAAAElJREFUeNpiYECA/UD8n0y8nwENUGIYTkOpAhihplMNMFHbhVQ3kIVA\ncOAD/0k1kAFP+DIO3UgZTYej6XA0HY6mw9F0CAEAAQYAk/gtCSEUikYAAAAASUVORK5CYII=\n",
  "content_type": "image/png",
  "file_name": "cabinet_icon.png"
}
```

To send the file using form data, set Content-Type as `multipart/form-data` and set the value of content as the binary part. Here's an example using `curl`:

```
curl -v -X POST -F "event=file" -F "content=@path/to/file.png" https://BASIC-AUTH@api.flowdock.com/flows/ORGANIZATION/FLOW/messages
```

Files can also be uploaded to discussion threads using the same URL format as with comments.

<div id="/thread-messages"></div>
### Threaded messages

There are a few types of messages that can belong to a [thread](threads):

 * [Activity](message-types#/activity) - updates to a thread
 * [Discussion](message-types#/discussion) - external discussions in another service
 * [Message](message-types#/message) - comments/discussion in Flowdock
 * [File](message-types#/file) - uploaded file stored in Flowdock

To post threaded messages to a flow, you need to have an application that has been authorized by the user. This process is described in [the production grade integration guide](production-integrations). If you just want to post messages to your own flows, have a look at the [the quick integration guide](integration-getting-started) for instructions on how to skip the authorization process and obtain a token posting messages.

<div id="/list"></div>
## List Messages
```
GET /flows/:organization/:flow/messages
GET /flows/:organization/:flow/threads/:id/messages
```
[URL breakdown](rest#/url-breakdown)

Lists messages from a flow, filtered by parameters.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| event | Filter messages by event type. Leave blank to get all messages, which is also the default. Multiple event types can be specified by separating them with commas. |
| limit | Maximum number of messages to return. Accepted values range from 1 to 100. Defaults to 30. |
| sort | Descending order is used by default. Use parameter value `asc` for ascending order. Note: the response JSON will always have the matching messages in ascending order by their `id`. The sort parameter is for toggling between fetching the latest or the oldest matching messages. |
| since\_id and until\_id | Get messages starting from or leading to a message `id`. The message specified with the parameter won't be included in the response. |
| tags | Filter messages by tags. Multiple tags can be specified by separating them with commas, which results in an AND search of those tags by default (see `tag_mode` parameter). When searching with user tags, you can use either the human-readable form, `@user`, or the internal form containing the user ID, `:user:12345`. Read more at [Tags](tags). |
| tag_mode | Toggles tag filtering of messages. By default the value is `and`. Use value `or` to search for messages that contain any of the tags specified in `tags`. |
| search | Full text search for message content. The search matches messages that contain all the specified keywords. Separate the keywords by spaces. |

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
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
    "user":"18",
    "created_at": "2011-09-30T15:44:45.507Z"
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
    "user": "2",
    "created_at": "2011-10-04T08:02:20.213Z"
  },
  // ... 28 more messages
]
```
Message fields are described below in the [Show Message](messages#/show) section.

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
```json
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
  "user":"18",
  "created_at": "2011-09-30T15:44:45.507Z"
}
```
| Name          | Description  |
| ------------- | ------------ |
| event | The type of the message. E.g. a chat message or tweet. See [Message Types](message-types). |
| content | Data payload. The format depends on the event type, as documented in [Message Types](message-types). |
| tags | List of tags as strings. Tags containing colon characters have a [special meaning](tags) and can be discarded when showing the tags in the user interface. |
| app | Deprecated. |
| uuid | Optional client-generated universal identifier for the message. Used to recognize messages sent by a client. |
| user | Numerical user id of user |
| sent | Timestamp when the message was sent. Milliseconds since Jan 1st, 1970. |
| flow | Flow identifier. |
| id | Incremental id of message. Unique only in the flow's scope. |
| attachments | List of file attachments related to this message. Example: |

```json
[
  {
    "content_type":"text/html",
    "content_id":"4e8b0e5bdccc2_4eec800c21ac57014@ciMac.local.mail",
    "file_name":"cucumber.html",
    "file_size":27798, // Size in bytes
    "path":"/files/123/ec53a7aa7a974cdc9d07c5ca395a2f05/cucumber.html"
  }
]
```

<div id="/edit"></div>
## Edit Message
```
PUT /flows/:organization/:flow/messages/:id
```
[URL breakdown](rest#/url-breakdown)

Updates a message with the specified id. Note: only certain types and certain content can be edited, as defined by the message event type.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| content | The message content. Updating content is only possible for your own messages of type `message` or `comment`. |
| tags | Full list of message [tags](tags). Any existing tags that aren't included in this parameter are removed from the message. As in the web UI, anyone can edit the tags of any message they can see. |

```json
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
```json
{}
```


<div id="/delete"></div>
## Delete Message
```
DELETE /flows/:organization/:flow/messages/:id
```
[URL breakdown](rest#/url-breakdown)

Delete a message with the specified id. Team inbox (`app=influx`) and a user's own file (`event=file`) messages will be completely removed and HTTP status 204 will be returned. User's own chat messages (`event=message` or `event=comment`) will be truncated with HTTP status 200. For any other messages the response will be HTTP status 403.

### Response
```
HTTP/1.1 204 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
