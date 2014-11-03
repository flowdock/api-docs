# Threads

Threads represent entities in external services. They allow users to track events that happen in external services and collect related events into a single view. Furthermore, all discussion related to the entity – both in Flowdock and the external service – is visible in the thread.

Common examples of thread entities are support tickets, issues in project management software and pull requests in version control software.

## Threads and messages

Threads can be updated by posting [messages](messages) to them. A basic update is done by sending an [activity message](message-types#/activity) into the thread. Any message sent into a thread can contain an updated thread in the `thread` field. The activity message will serve as a kind of a change log of changes in that thread. Because of this, there is no separate way to update a thread.

Likewise, any message, which is part of a thread, contains the full current state of that thread.


## Thread Format

```json
{
  "id": "x6BAmK_UPxKvtOSj1egQiVUma0s",
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
  "fields": [
    {
      "label": "assignee",
      "value": "<a href=\"mailto:antti@flowdock.com\" rel=\"nofollow\">Antti Pitkänen</a>"
    }
  ],
  "source": {
    "id": 104,
    "icon": "https://dxgv4vuja9avs.cloudfront.net/applications/104/64981f9198413209.png",
    "application": "GitHub"
  },
  "activities": 3,
  "internal_comments": 3,
  "external_comments": 5
}
```

| Name          | Description  |
| ------------- | ------------ |
| title | **Required** Title of the thread. It can contain following HTML tags: `a` |
| body | Body of the thread. HTML allowed. |
| external_url | URL to the external resource or entity that this thread represents, e.g. Github pull request or Rally user story. |
| status.value | Text value of the thread's status. |
| status.color | Background color for the above text value. Allowed values: red, green, yellow, cyan, orange, grey, black, lime, purple, blue |
| actions | An array of custom actions for the thread in [schema.org Action format](http://schema.org/Action). The only currently supported action is [ViewAction](http://schema.org/ViewAction). See the sample data above for an example of ViewAction. |
| fields | An array of key/value propertites for the thread. Value can contain the following HTML tags: `a` |
| source | The application that posted this thread. Includes the application name and icon. |
| activities | The number of [activity messages](message-types#/activity) in this thread. |
| internal_comments | The number of [chat messages](message-types#/message) in this thread. |
| external_comments | The number of [discussion messages](message-types#/discussion) in this thread. |


<div id="/list"></div>
## List Flow Threads

Lists threads from a flow, filtered by optional parameters.

```
GET /flows/:organization/:flow/threads
```

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| until | Return only threads updated before the specified ISO8601 date. |
| since | Return only threads updated after the specified ISO8601 date. |
| application | Only threads created by an application. This should be either OAuth application id, or one of special values `chat` or `ìnbox`. |
| empty | Include empty threads in response when empty is `1`. Defaults to `0`. |


### Response

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
Link  <https://api.flowdock.com/flows/example/main/threads?until=2014-10-27T14%3A29%3A36.855Z>; rel="next"
```

```json
[
  {
    "id": "x6BAmK_UPxKvtOSj1egQiVUma0s",
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
    "fields": [
      {
        "label": "assignee",
        "value": "<a href=\"mailto:antti@flowdock.com\" rel=\"nofollow\">Antti Pitkänen</a>"
      }
    ],
    "source": {
      "id": 104,
      "icon": "https://dxgv4vuja9avs.cloudfront.net/applications/104/64981f9198413209.png",
      "application": "GitHub"
    },
    "activities": 3,
    "internal_comments": 3,
    "external_comments": 5,
    "updated_at": "2014-10-28T15:49:31.197Z",
    "created_at": "2014-10-28T15:33:17.000Z"
  },
  ...
]
```

<div id="/show"></div>
## Get a Thread

Get a single thread.

```
GET /flows/:organization/:flow/threads/:id
```

### Response

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```

```json
{
  "id": "x6BAmK_UPxKvtOSj1egQiVUma0s",
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
  "fields": [
    {
      "label": "assignee",
      "value": "<a href=\"mailto:antti@flowdock.com\" rel=\"nofollow\">Antti Pitkänen</a>"
    }
  ],
  "source": {
    "id": 104,
    "icon": "https://dxgv4vuja9avs.cloudfront.net/applications/104/64981f9198413209.png",
    "application": "GitHub"
  },
  "activities": 3,
  "internal_comments": 3,
  "external_comments": 5,
  "updated_at": "2014-10-28T15:49:31.197Z",
  "created_at": "2014-10-28T15:33:17.000Z"
}
```