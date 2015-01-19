# Message types

This list explains the different types of messages you can expect when using the [Streaming API](streaming) or when [Listing Messages](messages#/list). Bear in mind that undocumented internal message types exist and the duty is left to the client to discard them properly.

Most messages can also be a part of a [thread](threads). In that case, the message data will also include a copy of the most recent up-to-date thread data in the `thread` field as well as the thread id in a separate `thread_id` field. Details are available in the [Messages documentation](messages#/thread-messages).

* [message](#/message)
* [status](#/status)
* [comment](#/comment)
* [action](#/action)
* [tag-change](#/tag-change)
* [message-edit](#/message-edit)
* [activity.user](#/activity-user)
* [file](#/file)
* [activity](#/activity)
* [discussion](#/discussion)

<div id="/message"></div>
## Event: message

The message event is sent when a user sends a chat message.

### Data

`content` is a string that contains free-form text.

### Sample
```json
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
}
```

<div id="/status"></div>
## Event: status

The status event is sent when a user changes their status.

### Data

`content` is a string that contains free-form text.

### Sample
```json
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
  "attachments": [],
  "created_at": "2011-10-04T08:02:20.213Z"
}
```

<div id="/comment"></div>
## Event: comment
> _DISCLAIMER: This message type is likely to change in the near future._

The comment event is sent when a user comments on an item in the team inbox.

### Data

`content` is an object that contains two keys: `title` is the team inbox item's title and `text`
contains the actual comment. The id of the parent message is stored in a [special tag](Tags):
`influx:id`.

### Sample
```json
{
  "app": "chat",
  "sent": 1317722877378,
  "uuid": "O-8aGb0fcc5gEgTX",
  "tags": ["influx:3838422"],
  "flow": "deadbeefdeadbeef",
  "id": 3838423,
  "event": "comment",
  "content": {
    "title": "Title of parent",
    "text": "This is a comment"
  },
  "user": "1609",
  "attachments": [],
  "created_at": "2011-10-04T10:07:57.378Z"
}
```

<div id="/action"></div>
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
```
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
  "attachments": [],
  "sent": "2011-09-30T15:44:45.507Z"
}
```

<div id="/tag-change"></div>
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
```
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
  "attachments": [],
  "created_at": "2011-09-30T15:44:45.507Z"
}
```

<div id="/message-edit"></div>
## Event: message-edit

The message-edit event is sent when the the content of a message is changed. Only messages of types `message` and `comment` can be edited.

### Data

`content` is an object with:

| Field          | Description  |
| ------------- | ------------ |
| message | The id of the message being updated. |
| updated_content | The new content of the message. |

### Sample
```
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
  "attachments": [],
  "created_at": "2011-09-30T15:44:45.507Z"
}
```

<div id="/activity-user"></div>
## Event: activity.user

> _DISCLAIMER: This message type is likely to change in the near future._

Activity.user is sent periodically by each user to let others know that they
are online.

These messages are not stored in Flowdock's database.

### Data

`content` is an object that contains a timestamp of a user's last activity. This
timestamp is not always present when e.g. user is idle.

### Sample
```
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
  "user": "2",
  "created_at": "2011-10-04T08:03:13.029Z"
}
```

<div id="/file"></div>
## Event: file
The file event is sent when a file has been uploaded to the chat.

### Data
`content` is an object that contains metadata about the uploaded file. The `attachments` field will contain a single attachment with the same data. In the metadata, the `path` field contains the REST API path of the file. See [Files](files) for more.

### Sample
```
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
  "tags": [":file"],
  "created_at": "2012-03-05T13:19:54.433Z"
}
```

<div id="/activity"></div>
## Event: activity

Activity messages represent events in external services. For example, that event could be a new user story for a project, an update to a support ticket or a commit to a repository. Activities are always related to some entity, which is represented by a [thread](Threads).

Comments in external services are not considered activities – they are represented by a separate [discussion](#/discussion) message type.

### Data

The attributes specific to this message type are explained below. For full reference on general message attributes, see [Message type](message-types#/message).

| Name          | Description  |
| ------------- | ------------ |
| **title** | Title of the activity. It can contain following HTML tags: `a` |
| body | Body of the activity. HTML is allowed. |
| **author.name** | The name of the author. |
| author.avatar | The URL of the author's avatar. |
| author.email | The email address of the author. |
| **thread_id** | Reference to the [thread](threads). |
| **thread** | The current state of the external entity, represented as a thread. See our [thread documentation](threads) for details on the fields. |
_Bolded text denotes a required field when posting a message._

### Sample

```
{
  "id": 13895827,
  "sent": 1411560930521,
  "event": "activity",
  "tags": [],
  "attachments": [],
  "flow": "ccc2899a-0485-4fd9-8706-e5bd28f03a9d",
  "author": {
    "name": "anttipitkanen",
    "avatar": "https://avatars.githubusercontent.com/u/946511?v=2"
  },
  "title": "Closed pull request",
  "body": null,
  "app": "influx",
  "user": "0",
  "edited": null,
  "content": null,
  "created_at": "2014-09-24T12:15:30.520Z",
  "_application": 104,
  "_project": 220,
  "thread_id": "HbjDdeOqrZm6QzlSO8rwtftzwts",
  "thread": {
    "title": "Fix bug in thread API",
    "body": "",
    "external_url": "https://github.com/flowdock/component/pull/42",
    "status": {
      "color": "grey",
      "value": "closed"
    },
    "actions": [
      {
        "@type": "ViewAction",
        "url": "https://github.com/flowdock/component/pull/42/files",
        "name": "Diff",
        "description": "View diff in GitHub",
        "image": "https://s3.amazonaws.com/flowdock-resources/images/diff.png"
      }
    ],
    "fields": [],
    "source": {
      "id": 104,
      "icon": "https://fd-files-eu-qa.s3.amazonaws.com/applications/104/ef8eff491304914a.png",
      "application": "GitHub"
    },
    "activities": 3,
    "internal_comments": 3,
    "external_comments": 5
  }
}
```

<div id="/discussion"></div>
## Event: discussion

Discussion is a chat message or comment that is made in an external service. It should be rendered similarly to Flowdock chat messages/comments. Examples of discussions include GitHub's pull request comments or replies to Zendesk tickets.

### Data

The attributes specific to this message type are explained below. For full reference on general message attributes, see the [message event](message-types#/message).

| Name          | Description  |
| ------------- | ------------ |
| **title** | The title of the activity. It can contain the following HTML tags: `a`  |
| body | The message body. HTML is allowed. |
| **author.name** | The name of the author. |
| author.avatar | The URL of the author's avatar. |
| author.email | The email address of the author. |
| **thread_id** | Reference to the [thread](threads). |
| **thread** | The current state of the external entity, represented as a thread. See our [thread documentation](threads) for details on the fields. |
_Bolded text denotes a required field when posting a message._

### Sample

```
{
  "id": 13904478,
  "sent": 1411632833971,
  "event": "discussion",
  "tags": [],
  "attachments": [],
  "uuid": "nudVKk0wy79MTMk6URqJfw",
  "flow":"ccc2899a-0485-4fd9-8706-e5bd28f03a9d",
  "author": {
    "name": "Antti Pitk\u00e4nen",
    "avatar": "https://secure.gravatar.com/avatar/0d7d028dd0eec07add85a3bba314b456?s=120&r=pg",
    "email":"antti@flowdock.com"
  },
  "title": "replied",
  "body": "Message body with HTML formatting &lt;br&gt;&lt;br&gt;",
  "app": "influx",
  "user": "0",
  "created_at": "2014-09-25T08:13:53.970Z",
  "edited": null,
  "content": null,
  "_application": 106,
  "_project":221,
  "thread_id": "zPEM5204zx5J9DwYI7BaU0eLk88",
  "thread": {
    "title": "Support request",
    "body": null,
    "external_url": "https://flowdock.zendesk.com/agent/#/tickets/6240",
    "status": {
      "value": "solved",
      "color": "grey"
    },
    "actions": [],
    "fields": [
      {
        "label": "Assignee",
        "value": "&lt;a href=\"mailto:antti@flowdock.com\" rel=\"nofollow\"&gt;Antti Pitk&auml;nen&lt;/a&gt;"
      },
      {
        "label": "Requester",
        "value": "&lt;a href=\"mailto:foo@example.com\" rel=\"nofollow\"&gt;John Smith&lt;/a&gt;"
      }
    ],
    "source": {
      "id": 106,
      "icon": "https://fd-files-eu-qa.s3.amazonaws.com/applications/106/6001f4814ff24487.png",
      "application": "Zendesk",
    },
    "activities": 1,
    "internal_comments": 2,
    "external_comments": 6
  }
}
```
