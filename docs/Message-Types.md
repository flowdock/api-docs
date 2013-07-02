# Message types

> DISCLAIMER: We are currently working on renewing and expanding both the message types documentation and the format of some of the message types. Thus, they are subject to change.

This list explains the different types of messages you can expect when using the [Streaming API](streaming) or [Listing Messages](messages#/list). Bear in mind that undocumented internal message types exist and the duty is left to the client to discard them properly.

## Event: message

The event type "message" is sent when user sends chat messages from Flowdock.

### Data

Message `content` is a string containing free-form text.

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

Status event is triggered when users change their statuses.

### Data

`content` is a string containing free-form text.

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
> DISCLAIMER: This message type is likely to change in the near future.

This message is sent when user comments an item in Team Inbox.

### Data

`content` is an object containing two keys: title for Team Inbox item title and text
containing the actual comment. Id of parent message is stored in a [special tag](Tags)
"influx:id".

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

Action may be triggered from various activities such as adding Twitter stream.

### Data

`content` is an object containing two keys: `type` containing the type of action
and `description` containing additional type-specific information. Data can be a
string or an object.

#### Action types

`type` is one of:

* `add_people` &ndash; Existing user belonging to the same account is added to the flow. `description` is list of nicks separated by commas (as string).

* `join` &ndash; User joins flow. User of message is the user who has just joined flow.

* `block` &ndash; User is blocked from flow. `description` is an object, where the "user" property is id of blocked user.

* `invite` &ndash; An invitation email is sent to user. `description` is an object containing the email addes in "email" property.

* `decline` &ndash; Invitation to join flow is declined. `description` is same as in `invite`.

* `uninvite` &ndash; An invitation is canceled by a member of the flow. `description` is similar to `invite`.

* `add_rss_feed` &ndash; RSS/Atom feed is added to flow. `description` is URL of feed.

* `remove_rss_feed` &ndash; RSS/Atom feed is removed from flow. `description` is the same as `add_rss_feed`.

* `add_twitter_follow` &ndash; Flow starts to follow a Twitter user. `description` is the name of Twitter user.

* `remove_twitter_follow` &ndash; Twitter user is unfollowed from flow. 'description' is same as in `add_twitter_follow`.

* `add_twitter_search` &ndash; A Twitter keyword search is added to flow. `description` contains the Twitter search term.

* `remove_twitter_search` &ndash; Twitter keyword is removed. `description` is the same as in `add_twitter_search`.

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
> DISCLAIMER: This message type is likely to change in the near future.

Used to change tags of messages. See [Tags](Tags) for more information about tags.

### Data

`content` is an object with

* `message` &nbsp; ID of the message being tagged
* `add` &nbsp; a list of tags added to message
* `remove` &nbsp; list of tags removed from message

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
> DISCLAIMER: This message is still a work in progress and likely to change in the near future.

Used to change content of messages. Only messages of types `message` and `comment` can be edited.

### Data

`content` is an object with:

* `message` &nbsp; ID of the message being updated
* `updated_content` &nbsp; new content of the message

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

> DISCLAIMER: This message type is likely to change in the near future.

Activity.user is sent periodically by each user to let others know that they
are online.

These messages are not stored to database.

### Data

`content` is an object, which contains a timestamp of last user activity. This
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
The file event represents a file upload to chat.

### Data
`content` is a hash containing metadata about the uploaded file. The `attachments` field will contain a single attachment with the same data. In the metadata, the `path` field contains the REST API path of the file. See [Files](files) for more.

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
      "path": "/flows/yup/main/files/d19d7d7048f3012fc1e40026b0d8e16c/screenshot.png",
      "file_name": "screenshot.png",
      "image": {
        "width": 1920,
        "height": 1087
      },
      "file_size": 554535,
      "content_type": "image/png",
      "thumbnail": {
        "path": "/flows/yup/main/files/d19d7d7048f3012fc1e40026b0d8e16c/thumb/screenshot.png",
        "width": 100,
        "height": 57
      }
    }
  ],
  "user": "9",
  "content": {
    "path": "/flows/yup/main/files/d19d7d7048f3012fc1e40026b0d8e16c/screenshot.png",
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
      "path": "/flows/yup/main/files/d19d7d7048f3012fc1e40026b0d8e16c/thumb/firefox_flowdock.png"
    }
  },
  "tags": [":file"]
}
```
