# Message types

> _DISCLAIMER: We are currently working on renewing and expanding both the message types documentation and the format of some of the message types. Thus, they are subject to change._

This list explains the different types of messages you can expect when using the [Streaming API](streaming) or when [Listing Messages](messages#/list). Bear in mind that undocumented internal message types exist and the duty is left to the client to discard them properly.

## Event: message

The message event is sent when a user sends a chat message.

### Data

`content` is a string that contains free-form text.

### Sample
```javascript
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
}
```

## Event: status

The status event is sent when a user changes their status.

### Data

`content` is a string that contains free-form text.

### Sample
```javascript
{
  "app": "chat",
  "sent": 1317307033981,
  "uuid": "anLhpBKqk0TXB27V",
  "tags": [],
  "flow": "deadbeefdeadbeef",
  "id": 3803707,
  "event": "status",
  "content": "This is my new status",
  "user": "49",
  "attachments": []
}
```

## Event: comment
> _DISCLAIMER: This message type is likely to change in the near future._

The comment event is sent when a user comments on an item in the team inbox.

### Data

`content` is an object that contains two keys: `title` is the team inbox item's title and `text`
contains the actual comment. The id of the parent message is stored in a [special tag](Tags):
`influx:id`.

### Sample
```javascript
{
  "app": "chat",
  "sent": 1317722877378,
  "uuid": "O-8aGb0fcc5gEgTX",
  "tags": ["influx:3838422"],
  "flow": "deadbeefdeadbeef",
  "id": 3838423,
  "event": "comment",
  "content": {
    "title": Title of parent",
    "text": "This is a comment"
  },
  "user": "1609",
  "attachments": []
}
```

## Event: action

Action events are sent by various activities, such as adding Twitter stream.

### Data

`content` is an object containing two keys: `type` defines the type of action
and `description` contains additional type-specific information. Data can be a
string or an object.

#### Action types

`type` is one of:

| Type          | Description  |
| ------------- | ------------ |
| add_people | An existing user or users of the organization are added to the flow. `description` is a comma-separated string of nicks. |
| join | A user joins the flow. The user who sent the message is the user who has just joined flow. |
| block | A user is blocked from the flow. `description` is an object whose `user` property is the id of the blocked user. |
| invite | An invitation email is sent to a user. `description` is an object whose `email` property contains the email address. |
| decline | An invitation to join the flow is declined. `description` is the same as in `invite`. |
| uninvite | An invitation is canceled by a member of the flow. `description` is the same as in `invite`. |
| add\_rss\_feed | An RSS/Atom feed is added to the flow. `description` is the URL of the feed. |
| remove\_rss\_feed | An RSS/Atom feed is removed from the flow. `description` is the same as in `add_rss_feed`. |
| add\_twitter\_follow | Start following a Twitter user in the flow. `description` is the username of the Twitter user. |
| remove\_twitter\_follow | Stop following a Twitter user in the flow. `description` is the same as in `add_twitter_follow`. |
| add\_twitter\_search | A Twitter keyword search is added to the flow. `description` contains the Twitter search term. |
| remove\_twitter\_search | A Twitter keyword is removed from the flow. `description` is the same as in `add_twitter_search`. |

### Sample
```javascript
{
  "app": "chat",
  "sent": 1317397485508,
  "uuid": "odHapx1VWp7WTrdQ",
  "tags": [],
  "flow": "deadbeefdeadbeef",
  "id": 3816534,
  "event": "action",
  "content": {
    "type": "add_twitter_search",
    "description": "flowdock"
  },
  "user": "18",
  "attachments": []
}
```


## Event: tag-change

The tag-change event is sent when the tags of a message are changed. See [Tags](Tags) for more information about tags.

### Data

`content` is an object with

| Field          | Description  |
| ------------- | ------------ |
| message | The id of the message being tagged. |
| add | A list of tags that were added to the message. |
| remove | A list of tags that were removed from the message. |

### Sample
```javascript
{
  "app": null,
  "sent": 1317397485508,
  "uuid": "odHapx1VWp7WTrdQ",
  "tags": [],
  "flow": "deadbeefdeadbeef",
  "id": 1235,
  "event": "tag-change",
  "content": {
    "message": 1234,
    "add": ["foo", "bar"],
    "remove": ["test"]
  },
  "user": "18",
  "attachments": []
}
```

## Event: message-edit

The message-edit event is sent when the the content of a message is changed. Only messages of types `message` and `comment` can be edited.

### Data

`content` is an object with:

| Field          | Description  |
| ------------- | ------------ |
| message | The id of the message being updated. |
| updated_content | The new content of the message. |

### Sample
```javascript
{
  "app": null,
  "sent": 1317397425508,
  "uuid": null,
  "tags": [],
  "flow": "deadbeefdeadbeef",
  "id": 1236,
  "event": "message-edit",
  "content": {
    "message": 1234,
    "updated_content": "foo bar"
  },
  "user": "18",
  "attachments": []
}
```

## Event: activity.user

> _DISCLAIMER: This message type is likely to change in the near future._

Activity.user is sent periodically by each user to let others know that they
are online.

These messages are not stored in Flowdock's database.

### Data

`content` is an object that contains a timestamp of a user's last activity. This
timestamp is not always present when e.g. user is idle.

### Sample
```javascript
{
  "event": "activity.user",
  "tags": [],
  "uuid": null,
  "id": 45592,
  "flow": "deadbeefdeadbeef",
  "content": {
    "last_activity": 1317715364447
  },
  "sent": 1317715393030,
  "app": null,
  "attachments": [],
  "user": '2'
}
```

<div id="/file"></div>
## Event: file
The file event is sent when a file has been uploaded to the chat.

### Data
`content` is an object that contains metadata about the uploaded file. The `attachments` field will contain a single attachment with the same data. In the metadata, the `path` field contains the REST API path of the file. See [Files](files) for more.

### Sample
```javascript
{
  "id": 31572,
  "app": "chat",
  "flow": "deadbeefdeadbeef",
  "event": "file",
  "sent": 1330953594433,
  "attachments": [
    {
      "path": "/files/123/d19d7d7048f3012fc1e40026b0d8e16c/screenshot.png",
      "file_name": "screenshot.png",
      "image": {
        "width": 1920,
        "height": 1087
      },
      "file_size": 554535,
      "content_type": "image/png",
      "thumbnail": {
        "path": "/files/123/d19d7d7048f3012fc1e40026b0d8e16c/thumb/screenshot.png",
        "width": 100,
        "height": 57
      }
    }
  ],
  "user": "9",
  "content": {
    "path": "/files/123/files/d19d7d7048f3012fc1e40026b0d8e16c/screenshot.png",
    "file_name": "screenshot.png",
    "image": {
      "width": 1920,
      "height": 1087
    },
    "file_size": 554535,
    "content_type": "image/png",
    "thumbnail": {
      "width": 100,
      "height": 57,
      "path": "/files/123/d19d7d7048f3012fc1e40026b0d8e16c/thumb/firefox_flowdock.png"
    }
  },
  "tags": [":file"]
}
```
