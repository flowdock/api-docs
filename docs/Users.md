# Users

A user in Flowdock can belong to multiple [organizations](Organizations) and [flows](Flows).

## List all users

List all users visible to the authenticated user, i.e. a combined set of users from all the organizations of the authenticated user. If the authenticated user is admin in the organization, all users are returned. Otherwise only users that are in the same flows as the authenticated user are returned.

```
GET /users
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
[
  {
    "id": 9,
    "email": "john@example.com",
    "name": "John Smith",
    "nick": "John",
    "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c/",
    "status": "Status message",
    "disabled": false,
    "last_activity": 1366613085178,
    "last_ping": 1366616861547
  },
  ...
]
```

* `id`: User resource ID

## List flow users

List users of a flow. Authenticated user must belong to the organization.

```
GET /flows/:organisation/:flow/users
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
[
  {
    "id": 9,
    "email": "john@example.com",
    "name": "John Smith",
    "nick": "John",
    "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c/",
    "status": "Status message",
    "disabled": false,
    "last_activity": 1366613085178,
    "last_ping": 1366616861547
  },
  ...
]
```

* `id`: User resource ID

## Get a user

Get information of a single user. Authenticated user must be belong to same organization as the target user.

```
GET /users/:id
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  "id": 9,
  "email": "john@example.com",
  "name": "John Smith",
  "nick": "John",
  "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c/",
  "status": "Status message",
  "disabled": false,
  "last_activity": 1366613085178,
  "last_ping": 1366616861547
}
```

## Update user information
```
PUT /users/:id
```
Update own user information.

### Input
* `nick`
  User's nick in chat.
* `email`
  User's email address.

```javascript
{
  "nick": "Steve",
  "email": "steve@example.com"
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

## Add a user to flow
```
POST /flows/:organization/:flow/users/:id
```
Add user to a flow. Authenticated user and the target user must be members of the organization.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{}
```


## Block a flow user
```
PUT /flows/:organization/:flow/users/:id
```
Blocks user from a flow. Organization admins can remove anyone (including other admins), but regular users can only remove other regular users.

### Input
* `disabled`
  Value `true` blocks user, `false` re-activates user

```javascript
{
  "disabled": true
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
