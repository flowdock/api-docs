# Users

A user in Flowdock can belong to multiple organizations and flows.

## List Users

List users of a flow.

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

## Get a User
```
GET /users/:id
```
Get a single user.

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

## Update User information
```
PUT /users/:id
```
Update user information.

### Input
* `nick`
  User's nick in chat.
* `email`
  User's email address.
* `first_name`
  User's first name.
* `last_name`
  User's last name.

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


## Add a User to flow
```
POST /flows/:organization/:flow/users/:id/add
```
Add user to a flow. The user must be member of the organization.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{}
```


## Block a User from flow
```
POST /flows/:organization/:flow/users/:id/block
```
Removes user from a flow. Organization admins can remove anyone (including other admins), but regular users can only remove other regular users.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{}
```
