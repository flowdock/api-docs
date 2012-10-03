# Users

A user in Flowdock can belong to multiple [organizations](Organizations) and [flows](Flows).

## List users

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
    "first_name": "John",
    "last_name": "Smith",
    "nick": "John",
    "avatar": "http://api.flowdock.com/avatars/667bc62d0ba333c684f0466d7a30a724/"
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
  "first_name": "John",
  "last_name": "Smith",
  "nick": "John",
  "avatar": "http://api.flowdock.com/avatars/667bc62d0ba333c684f0466d7a30a724/"
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
