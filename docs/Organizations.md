# Organizations

An organization in Flowdock can have multiple flows.

## List organizations

List organizations of the authenticated user.

```
GET /organizations
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 1
```
```
[
  {
    "id": "yup",
    "name": "My company",
    "user_limit": 80,
    "subscription":
    {
      "trial": true,
      "trial_ends": "2012-10-26"
    },
    "users": [
      {
        "id": 1,
        "name": "John Smith",
        "email": "john@example.com",
        "admin": true
      },
      {
        "id": 42,
        "name": "Stevie Johnson",
        "email": "stevie@example.com",
        "admin": false
      }
    ]
  },
  ...
]
```

* `id`: Organization resource ID
* `subscription`: information about the Flowdock subscription of the organization, contains `trial_ends` or `billing_date` depending on the value of `trial`

## Get an organization

Get information of an organization. Authenticated user must be belong to the organization.

```
GET /organizations/:id
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 1
```
```
{
  "id": "yup",
  "name": "My company",
  "user_limit": 80,
  "subscription":
  {
    "trial": true,
    "trial_ends": "2012-10-26"
  },
  "users": [
    {
      "id": 1,
      "name": "John Smith",
      "email": "john@example.com",
      "admin": true
    },
    {
      "id": 42,
      "name": "Stevie Johnson",
      "email": "stevie@example.com",
      "admin": false
    }
  ]
}
```

## Update organization information
```
PUT /users/:id
```
Update organization information. Only admins can modify organization information.

### Input
* `name`
  Organization's display name.

```javascript
{
  "name": "My company"
}
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 1
```
```
{}
```
