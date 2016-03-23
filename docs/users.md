# Users

A user in CA Flowdock can belong to multiple [organizations](organizations) and [flows](flows).

## List all users

List all users visible to the authenticated user, i.e. a combined set of users from all the organizations of the authenticated user. If the authenticated user is an admin in an organization, all of that organization's users are returned. Otherwise, only users that are in the same flows as the authenticated user are returned.

```
GET /users
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
[
  {
    "id": 9,
    "email": "john@example.com",
    "name": "John Smith",
    "nick": "John",
    "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c/",
    "website": "http://www.example.com/"
  },
  ...
]
```

## List flow users

List the users of a flow. The user must belong to the organization and have access to the flow.

```
GET /flows/:organization/:flow/users
```
[URL breakdown](rest#/url-breakdown)

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
[
  {
    "id": 9,
    "email": "john@example.com",
    "name": "John Smith",
    "nick": "John",
    "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c/",
    "website": "http://www.example.com/"
  },
  ...
]
```


## List an organization's users

List the users of an organization.

```
GET /organizations/:organization/users
```
[URL breakdown](rest#/url-breakdown)

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
[
  {
    "id": 9,
    "email": "john@example.com",
    "name": "John Smith",
    "nick": "John",
    "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c/",
    "website": "http://www.example.com/",
    "admin": false
  },
  ...
]
```

## Remove a user from an organization

To remove a user from an organization, you will need to have administrator rights to the organization.

```
DELETE /organizations/:organization/users/:id
```
[URL breakdown](rest#/url-breakdown)

### Response
```
HTTP/1.1 204 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```

## Get a user

Get information about a single user. The requesting user must be belong to same organization as the target user.

```
GET /users/:id
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
{
  "id": 9,
  "email": "john@example.com",
  "name": "John Smith",
  "nick": "John",
  "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c/",
  "website": "http://www.example.com/"
}
```

## Update user information
```
PUT /users/:id
```
Update the user's own information.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| nick | The user's nickname. |
| email | The user's email address. |

```json
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
```json
{}
```

## Add a user to flow
```
POST /flows/:organization/:flow/users
```
[URL breakdown](rest#/url-breakdown)

Add a user to a flow. The authenticated user and the target user must be members of the organization.

### Parameters
| Name          | Description  |
| ------------- | ------------ |
| id | The ID of the user to be added to the flow. |

```json
{
  "id": 42
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


## Block a user from a flow
```
PUT /flows/:organization/:flow/users/:id
```
[URL breakdown](rest#/url-breakdown)

Blocks a user from a flow. Organization admins can block anyone (including other admins), but regular users can only block other regular users.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| disabled | A boolean with a value of `true` to block the user, `false` to unblock them. |

```json
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
```json
{}
```
