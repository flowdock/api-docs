# Organizations

An organization in Flowdock represents the organization/company account. [Users](Users) can belong to several organizations.

Attribute `parameterized_name` is used in the URLs related to the organization. As `parameterized_name` can
be changed by the account owner, a persistent `id` is also provided in the data.

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
    "id": 42,
    "parameterized_name": "yup",
    "name": "My company",
    "user_limit": 80,
    "user_count": 2,
    "active": true,
    "url": "https://api.flowdock.com/organizations/yup",
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
* `parameterized_name`: Organization subdomain / alternative resource ID (can be changed)
* `user_limit`: maximum number of users with the current subscription
* `subscription`: information about the Flowdock subscription of the organization, contains `trial_ends` or `billing_date` depending on the value of `trial`

## Get an organization

Get information of an organization using the parameterized name. Authenticated user must belong to the organization.

```
GET /organizations/:parameterized_name
```
[URL breakdown](rest#/url-breakdown)

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 1
```
```
{
  "id": 42,
  "parameterized_name": "yup",
  "name": "My company",
  "user_limit": 80,
  "user_count": 2,
  "active": true,
  "url": "https://api.flowdock.com/organizations/yup",
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

## Get an Organization by id

```
GET /organizations/find?id=:id
```

Get information of an organization using the persistent organization id. Authenticated user
must belong to the organization. Return data is identical to getting an organization using
organization URL.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 1
```
```
{
  "id": 42,
  "parameterized_name": "yup",
  "name": "My company",
  "user_limit": 80,
  "user_count": 2,
  "active": true,
  "url": "https://api.flowdock.com/organizations/yup",
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

Update organization information. Only admins can modify organization information.

```
PUT /organizations/:parameterized_name
```
[URL breakdown](rest#/url-breakdown)

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
{
  "id": 42,
  "parameterized_name": "yup",
  "name": "My company",
  "user_limit": 80,
  "user_count": 2,
  "active": true,
  "url": "https://api.flowdock.com/organizations/yup",
  "subscription":
  {
    "trial": true,
    "trial_ends": "2013-01-27"
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
