# SCIM provisioning api

The Single Sign-On for Flowdock Enterprise offers partial support for provisioning using [System for Cross-Domain Identity Management (SCIM)](http://www.simplecloud.info/). To use the SCIM api, you need to be a Flowdock Enterprise customer and have the Single Sign-On configured. If you haven't completed these steps, please contact [support@flowdock.com](mailto:support@flowdock.com).

## Basics

The SCIM api is a REST based api that can be found under `https://api.flowdock.com/scim/:uid`. Uid is an organization specific identifier that can be acquired when setting up the SSO. All the resources can be found under that path.

The api uses JSON encoded data, so all requests need to be made using `application/json` content type and the data received back will be in the same format.

## Autentication

The SCIM api uses basic HTTP authentication using a bearer token. The token can be acquired during the SSO setup. When making requests to the SCIM api, you will need to provide the token in an authorization header. Given the authorization token `1b54iFrTbKIgP0Fl657cHA`, all of the requests to the api need a header:

```
Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
```

A missing authorization header will result in `401 Unauthorized` response. If the header is malformed or you use the wrong token, you will receive a `403 Forbidden` response.

You can test that you have a working authorization by making a request to the SCIM root url.

```
GET https://api.flowdock.com/scim/:uid
```

Response:

```
{
  "message": "Authorization ok"
}
```

Failed attempts are heavily ratelimited, so if you start receiving `429` responses, you need to wait for a while before trying again.

## Users resource

Users resource provides information about the users in your Flowdock organization. The api can only be used to access and modify users that have the SSO enabled, so if you have bots or other accounts that don't have SSO logins (like users that haven't yet migrated their account to use SSO), they won't show up here.

### SCIM identifiers

Flowdock assumes that the `userName` is the user's email address and uses that same email address as the `externalId`.

### Index

List all the SSO users in your organization:

```
GET https://api.flowdock.com/scim/:uid/Users
```

#### Request parameters

| Parameter | Description |
|-----------|-------------|
| **uid** | The uid for your SSO organization |

#### Response fields:

- **Resources** an array of user objects
    - **id** Id of the user object in Flowdock
    - **externalId** Extenal id of the user. Flowdock always reports this as the user's SSO email address.
    - **userName** User account name used to identify the user. Flowdock will always display this as the user's email address from the SSO provider.
    - **name** Information about the user's name.
        - **givenName** First name. As Flowdock only record's a user's full name, this will be the part of the name until the first space.
        - **familyName** Last name. This will be everything after the first space in a user's name.
    - **meta** SCIM metainformation
        - **location** The url for this user resource.
        - **created** When the user was created (date format ISO 8061)
        - **lastModified** When the user was last modified (date format ISO 8061)
    - **emails** An array with all known email addresses for the user. For each one:
        - **value** Contains the email address.
    - **nickName** Displayname of the user in Flowdock

#### Example request

```
GET https://api.flowdock.com/scim/123456/Users

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```

#### Response

```
{
  "Resources": [
    {
      "id": 1,
      "externalId":"johndoe@example.com",
      "userName":"johndoe@example.com",
      "name": {
        "givenName": "John",
        "familyName": "Doe"
      },
      "meta": {
        "location": "https://api.flowdock.com/scim/123456/Users/1",
        "created": "2013-03-12T10:36:38Z",
        "lastModified": "2015-01-15T14:51:45Z"
      },
      "emails": [
        {
          "value":  "johndoe@example.com"
        }
      ],
      "nickName":"John"
    }
  ]
}
```

### Search index (find by email)

The Users index accepts an optional filter parameter, that can be used to search for users. Currently only supported filter is `userName eq "email@example.com"`, which allows searching for users using their email address. The response is in the same format as with index.

#### Parameters

| Parameter| Description |
|----------|-------------|
| **filter** | A scim filter string. Only the `userName eq` filter is supported at the moment. |

#### Example request

```
GET https://api.flowdock.com/scim/123456/Users?filter=userName eq "email@example.com"

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```

### Show

A single user can be fetched using the Users show endpoint.

```
GET https://api.flowdock.com/scim/:uid/Users/:id
```

#### Parameters

| Parameter| Description |
|----------|-------------|
| **uid** | The uid for your SSO organization |
| **id** | Id of the Flowdock user |

The response is a single user object that has the same information as in index.

#### Example request

```
GET https://api.flowdock.com/scim/123456/Users/1

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```

#### Response

```
{
  "id": 1,
  "externalId":"johndoe@example.com",
  "userName":"johndoe@example.com",
  "name": {
    "givenName": "John",
    "familyName": "Doe"
  },
  "meta": {
    "location": "https://api.flowdock.com/scim/123456/Users/1",
    "created": "2013-03-12T10:36:38Z",
    "lastModified": "2015-01-15T14:51:45Z"
  },
  "emails": [
    {
      "value":  "johndoe@example.com"
    }
  ],
  "nickName":"John"
}
```

### Update

A single user object can be partially updated by doing a PUT to the user resource's location.

```
PUT https://api.flowdock.com/scim/:uid/Users/:id
```

#### Parameters

| Parameter| Description |
|----------|-------------|
| **uid** | The uid for your SSO organization |
| **id** | Id of the Flowdock user |

Payload must be a json document that contains the following parts from the full user representation:

- **name** Name that is split into first and last name. The api concatenates these and stores it in a single field.
    - **givenName** First name.
    - **familyName** Last name.
- **emails** An array of email objects. Only the first one is used. For that:
    - **value** Contains the email address.
- **nickName** (optional) the displayname for the user in Flowdock.

#### Example request

```
PUT https://api.flowdock.com/scim/123456/Users/1

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json

{
  "name": {
    "givenName": "New",
    "familyName": "Name"
  },
  "emails": [
    {
      "value":  "new.john@example.com"
    }
  ],
  "nickName":"NewJohn"
}

```

The response will contain the full information for the user.

### Destroy

Destroy can be used to deprovision a user. The user will be removed from the organization immediately and will lose all access to the organization's data.

```
DELETE https://api.flowdock.com/scim/:uid/Users/:id
```

#### Parameters

| Parameter| Description |
|----------|-------------|
| **uid** | The uid for your SSO organization |
| **id** | Id of the Flowdock user |

A successfull response will be a `204 No Content`.

#### Example request

```
DELETE https://api.flowdock.com/scim/123456/Users/1

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```

### Create

Creating users is not supported and will result in a `405 Method Not Allowed` response.
