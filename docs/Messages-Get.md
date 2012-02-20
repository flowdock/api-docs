# List Messages

Lists messages from a flow, filtered by parameters.

```
GET /messages
```

## Parameters

* `event` &ndash; Filter messages by event type. Leave blank to get all messages, which is also the default.

* `limit` &ndash; Maximum number of messages

* `since_id` and `until_id` &ndash; Get messages starting from or leading to a message. The message specified with the parameter won't be included in the response. 

## Response

```
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
    "user":"18","attachments":[]
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

* `tags` &ndash; List of tags as strings. Tags containing colon characters have special meaning and can be discarded when working with tagging user interfaces.

* `app` &ndash; Deprecated.

* `uuid` &ndash; Optional client-generated universal identifier for message. Used to recognize messages sent by single Flowdock instance. Can be used to render sent messages instantly and later add necessary data (id) for tagging.

* `user` &ndash; Numerical user id of user (as string)

* `sent` &ndash; Timestamp when message was sent. Milliseconds since Jan 1st, 1970.

* `flow` &ndash; Identifier of flow. See [Flows](Flows).

* `id` &ndash; Incremental if of message. Unique only in flow scope.

* `attachments` &ndash; List of file attachments related to this message.

```
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

* `event` &ndash; The type of the message. E.g. chat message or tweet.

* `content` &ndash; Data payload. Different in different events as documented below.


## Message types

The different event types are as follows.

### Event: message

The event type "message" is sent when user sends chat messages from Flowdock.

#### Data

Message `content` is a string containing free-form text.

#### Sample

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
    }

### Event: comment

This message is sent when user comments an item in Team Inbox.

#### Data

`content` is an object containing two keys: title for Team Inbox item title and text
containing the actual comment. Id of parent message is stored in a special tag
"influx:id".

#### Sample

    {
      "app":"chat",
      "sent":1317722877378,
      "uuid":"O-8aGb0fcc5gEgTX",
      "tags":["influx:3838422"],
      "flow":"subdomain:flow",
      "id":3838423,
      "event":"comment",
      "content": {
        "title": Title of parent",
        "text":"This is a comment"
      },
      "user":"1609",
      "attachments":[]
    }


## Event: action

Action may be triggered from various activities such as adding Twitter stream.

### Data

`content` is an object containing two keys: `type` containing the type of action
and `description` containing additional type-specific information. Data can be a
string or an object.

### Action types

`type` is one of:

* `add_people` &ndash; Existing user belonging to the same account is added to the flow. `description` is list of nicks separated by commas (as string).

* `join` &ndash; User joins flow. User of message is the user who has just joined flow.

* `block` &ndash; User is blocked from flow. `description` is an object, where the "user" property is id of blocked user.

* `invite` &ndash; An invitation email is sent to user. `description` is an object containing the email addes in "email" property.

* `decline` &ndash; Invitation to join flow is declined. `description` is same as in `invite`.

* `uninvite` &ndash; An invitation is canceled by a member of the flow. `description` is similar to `invite`.

* `add_rss_feed` &ndash; RSS/Atom feed is added to flow. `desciption` is URL of feed.

* `remove_rss_feed` &ndash; RSS/Atom feed is removed from flow. `description` is the same as `add_rss_feed`.

* `add_twitter_follow` &ndash; Flow starts to follow a Twitter user. `description` is the name of Twitter user.

* `remove_twitter_follow` &ndash; Twitter user is unfollowed from flow. 'description' is same as in `add_twitter_follow`.

* `add_twitter_search` &ndash; A Twitter keyword search is added to flow. `description` contains the Twitter search term.

* `remove_twitter_search` &ndash; Twitter keyword is removed. `description` is the same as in `add_twitter_search`.

#### Sample

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
      "user":"18","attachments":[]
    }

### Event: status

Status event is triggered when users change their statuses.

#### Data

`content` is a string containing free-form text.

#### Sample

    {
      "app":"chat",
      "sent":1317307033981,
      "uuid":"anLhpBKqk0TXB27V",
      "tags":[],
      "flow":"nodeta:flowdock",
      "id":3803707,
      "event":"status",
      "content":"This is my new status",
      "user":"49",
      "attachments":[]
    }

### Event: tag-change

Used to change tags of messages.

#### Data

`content` is an object with
  * `message` &nbsp; ID of the message being tagged
  * `add` &nsbp; a list of tags added to message
  * `remove` &nbsp; list of tags removed from message

#### Sample

    {
      "app":null,
      "sent":1317397485508,
      "uuid":"odHapx1VWp7WTrdQ",
      "tags":[],
      "flow": "subdomain:sample-flow",
      "id":1235,
      "event":"tag-change",
      "content":{
        "message": 1234,
        "add": ["foo", "bar"],
        "remove": ["lol"]
      },
      "user":"18",
      "attachments":[]
    }

### Event: activity.user

Activity.user is sent periodically by each user to let others know that they
are online.

These messages are not stored to database.

#### Data

`content` is an object, which contains a timestamp of last user activity. This
timestamp is not always present when e.g. user is idle.

#### Sample

    {
      "event": "activity.user",
      "tags": [],
      "uuid": null,
      "id": 45592,
      "flow": 'lautixentestiflowi',
      "content": {
        "last_activity": 1317715364447
      },
      "sent": 1317715393030,
      "app": null,
      "attachments": [],
      "user": '2'
    }


### Event: backend.join.user

Backend.join.user is sent when a new user is added to flow. This is sent before
any other possible join notifications. *Deprecated*: may be removed in future versions.

#### Data

`content` contains the user object.

#### Sample

    {
      "app":null,
      "sent":1306481608147,
      "tags":[],
      "flow": "subdomain:sample-flow",
      "id":3144297,
      "event":"backend.join.user",
      "content":{
          "user":{
              "last_activity":null,
              "nick":"Marty",
              "avatar":"https://secure.gravatar.com/avatar/94a5b5f3eb9a8ed1f34ca3ac42b78769?s=30&d=https%3A%2F%2Fstatic.flowdock.com%2Fimages%2Fdefault_user_icon_30.png",
              "id":2873,
              "last_name":"McFly",
              "first_name":"Marty",
              "email":"marty@example.com"
            }
      },
      "user":"2873",
      "attachments":[]
    }
