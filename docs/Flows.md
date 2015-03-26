# Flows

A flow in Flowdock is a team workspace that contains a chat room and a shared inbox.

<div id="/list-flows"></div>
## List Flows
Lists the flows that the authenticated user is a member of.

By default, the list of flows only includes the flows that the user is a member of (i.e. the `joined` attribute is `true`). For flows with an `access_mode` of `organization`, the user must explicitly join the flow in order to be considered a member. For these flows, the `joined` attribute is initially `false`. A separate "all flows" resource exists in the API to list them.

```
GET /flows
```

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| users | A boolean value (1/0) that controls whether a list of users should be included with each flow. |

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
[
  {
    "id": "deadbeefdeadbeef",
    "name": "My flow",
    "parameterized_name": "my-flow",
    "organization": {
      "id": 42,
      "name": "Example",
      "parameterized_name": "example",
      "user_limit": 0,
      "user_count": 10,
      "active": true,
      "url": "https://api.flowdock.com/organizations/example"
    }
    "unread_mentions": 0,
    "open": true,
    "joined": true,
    "url": "https://api.flowdock.com/flows/acme/my-flow",
    "web_url": "https://www.flowdock.com/app/acme/my-flow",
    "join_url": "https://www.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
    "access_mode": "organization",
  },
  {
    "id": "05654aa3-3b13-4a79-8cea-92ed45bdc9a5",
    "name": "Another flow",
    "parameterized_name": "another-flow",
    "organization": {
      "id": 8,
      "name": "Acme",
      "parameterized_name": "acme",
      "user_limit": 0,
      "user_count": 5,
      "active": true,
      "url": "https://api.flowdock.com/organizations/acme"
    }
    "unread_mentions": 0,
    "open": true,
    "joined": true,
    "url": "https://api.flowdock.com/flows/acme/another-flow",
    "web_url": "https://www.flowdock.com/app/acme/another-flow",
    "access_mode": "invitation",
  },
]
```

| Name          | Description  |
| ------------- | ------------ |
| id | The flow's id. |
| url | The flow's resource URL. |
| web_url | The flow's web UI URL. |
| name | Human-readable name of the flow. |
| organization | [Organization](Organizations) information. |
| unread_mentions | Count of unread messages that mention the authenticated user. |
| open | Boolean value (true or false). Clients implementing tabs should display tabs for all the `open` flows, and list non-open flows elsewhere. |
| joined | Boolean value (true or false). If false, the user must explicitly join the flow to gain access to it. This can be done with a `PUT` request that sets the `open` attribute of the flow to `true`. |
| join_url | URL where new users can join the flow if `access_mode` is `organization` or `link`. Only present if `access_mode` is `organization` or `link`. |
| access_mode | How users see and access the flow. Possible values are `invitation` (the flow is invite-only – new members have to be explicitly invited or added by existing members), `link` (anyone can join the flow by using the `join_url`) or `organization` (in addition to using the link, anyone in the organization can join the flow.) |


**Note**: Flow IDs have historically been in the form `organization:flow`, but new flows are created with random IDs.

## List all flows
Lists the flows that the authenticated user has access to or can join.

Includes all the flows from the "list flows" resource as well as the flows with an `access_mode` of `organization` that the user has not joined (`joined` is `false`).

```
GET /flows/all
```

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| users | A boolean value (1/0) that controls whether a list of users should be included with each flow. |

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
[
  {
    "id": "deadbeefdeadbeef",
    "name": "My flow",
    "parameterized_name": "my-flow",
    "organization": {
      "id": 8,
      "name": "Acme",
      "parameterized_name": "acme",
      "user_limit": 0,
      "user_count": 5,
      "active": true,
      "url": "https://api.flowdock.com/organizations/acme"
    }
    "unread_mentions": 0,
    "open": false,
    "joined": false,
    "url": "https://api.flowdock.com/flows/acme/my-flow",
    "web_url": "https://www.flowdock.com/app/acme/my-flow",
    "join_url": "https://www.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
    "access_mode": "organization",
  },
  {
    "id": "05654aa3-3b13-4a79-8cea-92ed45bdc9a5",
    "name": "Another flow",
    "parameterized_name": "another-flow",
    "organization": {
      "id": 8,
      "name": "Acme",
      "parameterized_name": "acme",
      "user_limit": 0,
      "user_count": 5,
      "active": true,
      "url": "https://api.flowdock.com/organizations/acme"
    }
    "unread_mentions": 0,
    "open": true,
    "joined": true,
    "url": "https://api.flowdock.com/flows/acme/another-flow",
    "web_url": "https://www.flowdock.com/app/acme/another-flow",
    "access_mode": "invitation",
  },
]
```

*Flow fields are described above in the [List flows section](flows#/list-flows).*

## Get a flow
```
GET /flows/:organization/:flow
```
[URL breakdown](rest#/url-breakdown)

Get a single flow. Single flow information always includes the flow's user list. Otherwise, the data format is identical to what is returned with the list of flows. Timestamps are milliseconds since epoch.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
{
  "id": "deadbeefdeadbeef",
  "name": "My flow",
  "parameterized_name": "my-flow",
  "organization": {
    "id": 8,
    "name": "Acme",
    "parameterized_name": "acme",
    "user_limit": 0,
    "user_count": 5,
    "active": true,
    "url": "https://api.flowdock.com/organizations/acme"
  }
  "unread_mentions": 0,
  "open": true,
  "url": "https://api.flowdock.com/flows/acme/my-flow",
  "web_url": "https://www.flowdock.com/app/acme/my-flow",
  "join_url": "https://www.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
  "access_mode": "link",
  "users": [
    {
      "id": 9,
      "nick": "Joe",
      "name": "Joe Smith",
      "email": "joe@example.com",
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f5b8fb60c6116331da07c65b96a8a1d1/",
      "status": "Testing API",
      "disabled": false,
      "last_activity": 1328016726423000,
      "last_ping": 1328017690004000
    },
    {
      "id": 42,
      "nick": "Stevie",
      "name": "Stevie Johnson",
      "email": "stevie@example.com",
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/5bdd089a099acc56fc7120f6325a5d5c/",
      "status": null,
      "disabled": true,
      "last_activity": 1328016712345000,
      "last_ping": 1328017612345000
    }
  ]
}
```

## Get a flow by id

```
GET /flows/find?id=:id
```

Get a single flow using the flow's id. The returned data is identical to getting a flow with the flow's URL.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
{
  "id": "deadbeefdeadbeef",
  "name": "My flow",
  "parameterized_name": "my-flow",
  "organization": {
    "id": 8,
    "name": "Acme",
    "parameterized_name": "acme",
    "user_limit": 0,
    "user_count": 5,
    "active": true,
    "url": "https://api.flowdock.com/organizations/acme"
  }
  "unread_mentions": 0,
  "open": true,
  "url": "https://api.flowdock.com/flows/acme/my-flow",
  "web_url": "https://www.flowdock.com/app/acme/my-flow",
  "join_url": "https://www.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
  "access_mode": "link",
  "users": [
    {
      "id": 9,
      "nick": "Joe",
      "name": "Joe Smith",
      "email": "joe@example.com",
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f5b8fb60c6116331da07c65b96a8a1d1/",
      "status": "Testing API",
      "disabled": false,
      "last_activity": 1328016726423000,
      "last_ping": 1328017690004000
    },
    {
      "id": 42,
      "nick": "Stevie",
      "name": "Stevie Johnson",
      "email": "stevie@example.com",
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/5bdd089a099acc56fc7120f6325a5d5c/",
      "status": null,
      "disabled": true,
      "last_activity": 1328016712345000,
      "last_ping": 1328017612345000
    }
  ]
}
```


## Create a flow
```
POST /flows/:organization
```
[URL breakdown](rest#/url-breakdown)

Create a flow for the specified organization.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| name | The name of the new flow (max. 100 characters). |

```javascript
{
  "name": "My flow"
}
```

### Response
```
HTTP/1.1 201 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 9
```
```json
{
  "id": "deadbeefdeadbeef",
  "name": "My flow",
  "parameterized_name": "my-flow",
  "organization": {
    "id": 8,
    "name": "Acme",
    "parameterized_name": "acme",
    "user_limit": 0,
    "user_count": 5,
    "active": true,
    "url": "https://api.flowdock.com/organizations/acme"
  }
  "unread_mentions": 0,
  "open": true,
  "url": "https://api.flowdock.com/flows/acme/my-flow",
  "web_url": "https://www.flowdock.com/app/acme/my-flow",
  "access_mode": "invitation",
  "users": [
    {
      "id": 9,
      "nick": "Joe",
      "name": "Joe Smith",
      "email": "joe@example.com",
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f5b8fb60c6116331da07c65b96a8a1d1/",
      "status": "Testing API",
      "disabled": false,
      "last_activity": 1328016726423000,
      "last_ping": 1328017690004000
    }
  ]
}
```

## Update a flow
```
PUT /flows/:organization/:flow
```
[URL breakdown](rest#/url-breakdown)

Update flow information. Only adminstrators can modify certain parts of the flow metadata.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| name | New name of the flow (max. 100 characters). Note that changing name does not change the flow's email address or id. |
| open | Boolean value (true or false). If set to `true` and the user has not previously joined the flow, the user will automatically be  added to the flow. |
| disabled | Boolean value (true or false). When set to true, the flow will disappear from users, users will no longer be able to send  messages to it and users will not be able to join the flow. The flow can be considered to be archived. |
| access_mode | How users see and access the flow. Possible values are `invitation` (the flow is invite-only – new members have to be explicitly invited or added by existing members), `link` (anyone can join the flow by using the `join_url`) or `organization` (in addition to using the link, anyone in the organization can join the flow.) |

```javascript
{
  "name": "My new flow",
  "disabled": false,
  "access_mode": "organization"
}
```
### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 9
```
```json
{
  "id": "deadbeefdeadbeef",
  "name": "My new flow",
  "parameterized_name": "my-flow",
  "organization": {
    "id": 8,
    "name": "Acme",
    "parameterized_name": "acme",
    "user_limit": 0,
    "user_count": 5,
    "active": true,
    "url": "https://api.flowdock.com/organizations/acme"
  }
  "unread_mentions": 0,
  "open": true,
  "joined": true,
  "url": "https://api.flowdock.com/flows/acme/my-flow",
  "web_url": "https://www.flowdock.com/app/acme/my-flow",
  "join_url": "https://www.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
  "access_mode": "organization",
}
```
