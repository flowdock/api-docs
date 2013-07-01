# Flows

A flow in Flowdock is like one single team workspace, which contains a chat room and a shared inbox.

## List Flows
Lists the flows that the authenticated user has access to. E.g. `joined` attribute of the flow is `true`.

By default, the list of flows only includes those flows that the user has access to (`joined` attribute is `true`). However, if the access mode of a flow is set to `organization`, the user must explicitly join the flow to gain access. For these flows, the `joined` attribute is initially `false` and a separate "all flows" resource exists in the API for including them.

```
GET /flows
```

### Parameters

* `users`: boolean value (1/0) controlling whether a list of users should be included in for each flow.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
[
  {
    "id": "deadbeefdeadbeef",
    "name": "My flow",
    "organization": "Acme",
    "unread_mentions": 0,
    "open": true,
    "joined": true,
    "url": "https://api.flowdock.com/flows/acme/my-flow",
    "web_url": "https://acme.flowdock.com/flows/my-flow",
    "join_url": "https://acme.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
    "access_mode": "organization",
  },
  {
    "id": "anotherflow",
    "name": "Another flow",
    "organization": "Acme",
    "unread_mentions": 0,
    "open": true,
    "joined": true,
    "url": "https://api.flowdock.com/flows/acme/another-flow",
    "web_url": "https://acme.flowdock.com/flows/another-flow",
    "access_mode": "invitation",
  },
]
```

* `id`: Flow ID
* `url`: Flow resource URL
* `web_url`: URL to the flow in the web UI
* `name`: Human-readable name of the flow
* `organization`: Human-readable name of the organization
* `unread_mentions`: Count of unread messages that mention the authenticated user
* `open`: Boolean value (true or false). Clients implementing tabs should display tabs for all the `open` flows, and list non-open flows elsewhere.
* `joined`: Boolean value (true or false). If false, the user must explicitly join the flow to gain access to it. This can be done with a `PUT` request that sets the `open` attribute of the flow to `true`.
* `join_url`: URL where new users can join the flow if `access_mode` is `organization` or `link`. Only present if `access_mode` is `organization` or `link` false.
* `access_mode`: How users see and access the flow. Possible values are:
    - `invitation`: Flow is invite only. New members have to be explicitly invited or added by existing members.
    - `link`: Anyone can join the flow by using the `join_url`.
    - `organization`: In addition to using the link, anyone in the organization can join the flow (it will be visible for them).


**Note**: Flow IDs have historically been in the form `organization:flow`, but
new flows are created with random IDs.

## List all Flows
Lists the flows that the authenticated user has access to or can join to.

Includes all the flows from the "list flows" resource, and those flows that have `organization` as `access_mode` and the user has not yet joined (`joined` is `false`).

```
GET /flows/all
```

### Parameters

* `users`: boolean value (1/0) controlling whether a list of users should be included in for each flow.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
[
  {
    "id": "deadbeefdeadbeef",
    "name": "My flow",
    "organization": "Acme",
    "unread_mentions": 0,
    "open": false,
    "joined": false,
    "url": "https://api.flowdock.com/flows/acme/my-flow",
    "web_url": "https://acme.flowdock.com/flows/my-flow",
    "join_url": "https://acme.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
    "access_mode": "organization",
  },
  {
    "id": "acme/another-flow",
    "name": "Another flow",
    "organization": "Acme",
    "unread_mentions": 0,
    "open": true,
    "joined": true,
    "url": "https://api.flowdock.com/flows/acme/another-flow",
    "web_url": "https://acme.flowdock.com/flows/another-flow",
    "access_mode": "invitation",
  },
]
```

* Flow fields are described above in the List flows section.

## Get a Flow
```
GET /flows/:organization/:flow
```
Get a single flow. Single flow information always includes user list of flow. Otherwise the data format is identical to the list. Timestamps are milliseconds since epoch.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  "id": "deadbeefdeadbeef",
  "name": "My flow",
  "organization": "Acme",
  "unread_mentions": 0,
  "open": true,
  "url": "https://api.flowdock.com/flows/acme/my-flow",
  "web_url": "https://acme.flowdock.com/flows/my-flow",
  "join_url": "https://acme.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
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

## Get a Flow by Id

```
GET /flows/find?id=:id
```

Get a single flow using flow id. Return data is identical to getting a flow
using flow URL.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  "id": "deadbeefdeadbeef",
  "name": "My flow",
  "organization": "Acme",
  "unread_mentions": 0,
  "open": true,
  "url": "https://api.flowdock.com/flows/acme/my-flow",
  "web_url": "https://acme.flowdock.com/flows/my-flow",
  "join_url": "https://acme.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
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


## Create a Flow
```
POST /flows/:organization
```
Create a flow for the specified organization.

### Input

* `name`: name of the flow, max. 100 characters

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
```
{
  "id": "deadbeefdeadbeef",
  "name": "My flow",
  "organization": "Acme",
  "unread_mentions": 0,
  "open": true,
  "url": "https://api.flowdock.com/flows/acme/my-flow",
  "web_url": "https://acme.flowdock.com/flows/my-flow",
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

## Update a Flow
```
PUT /flows/:organization/:flow
```
Update flow information. Only admins can modify certain parts of the flow information.

### Input

* `name`: New name of the flow, max. 100 characters. Note that changing name does not change flow's email or id
* `open`: Boolean value (true or false). If set to `true` and the user has not previously joined the flow, the user will automatically be added to the flow.
* `disabled`: Boolean value (true or false). Controlling if the flow is disabled
* `access_mode`: Controls access mode of the flow. Possible values are:
    - `invitation`: Flow is invite only. New members have to be explicitly invited or added by existing members.
    - `link`: Anyone can join the flow by using the `join_url`.
    - `organization`: In addition to using the link, anyone in the organization can join the flow (it will be visible for them).

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
```
{
  "id": "deadbeefdeadbeef",
  "name": "My new flow",
  "organization": "Acme",
  "unread_mentions": 0,
  "open": true,
  "joined": true,
  "url": "https://api.flowdock.com/flows/acme/my-flow",
  "web_url": "https://acme.flowdock.com/flows/my-flow",
  "join_url": "https://acme.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow",
  "access_mode": "organization",
}
```
