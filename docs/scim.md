# SCIM provisioning API

The Single Sign-On for CA Flowdock Enterprise offers partial support for provisioning using [System for Cross-Domain Identity Management (SCIM)](http://www.simplecloud.info/).

To use the SCIM API, you need to be a CA Flowdock Enterprise customer and have Single Sign-On configured for your organization. Visit our [community](https://communities.ca.com/community/ca-agile-central/ca-flowdock) page for support.

## Basics

The SCIM API is a REST-based API that can be found under `https://api.flowdock.com/scim/:uid`. The UID is an organization-specific identifier that is acquired when setting up SSO. All the resources can be found under this path.

All requests need to be made using a content-type of `application/json`. Responses will be sent in JSON format.

## Authentication

The SCIM API uses basic HTTP authentication using a bearer token. The token is acquired during the SSO setup. When making requests to the SCIM API, you will need to provide the token in an authorization header. Given the authorization token `1b54iFrTbKIgP0Fl657cHA`, all of the requests to the API need a header:

```
Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
```

A missing authorization header will result in a `401 Unauthorized` response. If the header is malformed or has the wrong token, you will receive a `403 Forbidden` response.

You can test your authorization by making a request to the SCIM root URL.

#### Request
```
GET https://api.flowdock.com/scim/:uid
```

#### Response
```json
{
  "message": "Authorization ok"
}
```

Failed attempts are heavily rate-limited. If you start receiving `429` responses, you will need to wait for a while before trying again.

## Users resource

The users resource provides information about the users in your CA Flowdock organization. The API can only be used to access and modify users that have SSO enabled. Accounts that don't have a linked SSO identity (such as bots) will not be listed.

### SCIM identifiers

CA Flowdock assumes that the `userName` is the user's email address and uses that same email address as the `externalId`.

### Index

List all the SSO users in your organization:

```
GET https://api.flowdock.com/scim/:uid/Users
```

#### Request parameters

| Parameter | Description |
|-----------|-------------|
| **uid** | The UID of your SSO organization. |

#### Response fields:

- **Resources** An array of user objects.
    - **id** The ID of the user object in CA Flowdock.
    - **externalId** The extenal id of the user. It has the same value as `userName`.
    - **userName** The user account name used to identify the user. This will be the Subject's NameID passed by the SAML provider.
    - **name** Information about the user's name.
        - **givenName** First name. As CA Flowdock only record's a user's full name, this will be the part of the name that precedes the first space.
        - **familyName** Last name. This is everything after the first space in a user's name.
    - **meta** SCIM metadata.
        - **location** The URL for this user resource.
        - **created** When the user was created (using the ISO 8061 format).
        - **lastModified** When the user was last modified (using the ISO 8061 format).
    - **emails** An array with all known email addresses for the user. For each one:
        - **value** Contains the email address.
    - **nickName** The display name (nickname) of the user in CA Flowdock.

#### Example request

```
GET https://api.flowdock.com/scim/123456/Users

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```

#### Response

```json
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
      "nickName":"JohnD"
    }
  ]
}
```

### Search

The Users index accepts an optional filter parameter that can be used to search for users. Currently, the only supported filters are `userName eq "email@example.com"` and `emails eq "email@example.com"`. This allows you to search for users using their login or email address. The response is in the same format as in the user index.

#### Parameters

| Parameter| Description |
|----------|-------------|
| **uid** | The UID of your SSO organization. |
| **filter** | A SCIM filter string. Only the `userName eq` and `emails eq` filters are supported. |

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
| **uid** | The UID of your SSO organization. |
| **id** | The ID of the CA Flowdock user. |

The response is a single user object in the same format as in the user index.

#### Example request

```
GET https://api.flowdock.com/scim/123456/Users/1

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```

#### Response

```json
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
  "nickName":"JohnD"
}
```

### Update

A single user object can be partially updated by sending a PUT to the user resource's location.

```
PUT https://api.flowdock.com/scim/:uid/Users/:id
```

#### Parameters

| Parameter| Description |
|----------|-------------|
| **uid** | The UID of your SSO organization. |
| **id** | The ID of the CA Flowdock user. |

The payload must be a JSON document that contains the following parts from the full user representation:

- **name** The full name that is split into first and last name. The API concatenates these and stores them in a single field.
    - **givenName** First name.
    - **familyName** Last name.
- **emails** An array of email objects. Only the first one is used. For that:
    - **value** Contains the email address.
- **nickName** (optional) The display name of the user in CA Flowdock.
- **active** (optional) Supply a value of `false` to log out all of user's sessions. This can be used when the user is suspended in the SSO provider, and will have the effect of suspending the user in CA Flowdock too, since the user is forced to reauthenticate, which in turn requires using the SSO provider.

#### Example request

```
PUT https://api.flowdock.com/scim/123456/Users/1

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```
```json
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

Alternatively, a `400 Bad request` response might be returned if the user validation fails.

### Destroy

Destroy can be used to deprovision a user. The user will be removed from the organization and any child organizations immediately. The user will lose all access to the organization's data, including flows and private conversations.

```
DELETE https://api.flowdock.com/scim/:uid/Users/:id
```

#### Parameters

| Parameter| Description |
|----------|-------------|
| **uid** | The UID of your SSO organization. |
| **id** | The ID of the CA Flowdock user. |

A response to a successful request is `204 No Content`.

#### Example request

```
DELETE https://api.flowdock.com/scim/123456/Users/1

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```

The response will be `200 OK` with a json document containing "message".

### Create

Users can be created using SCIM.

```
POST https://api.flowdock.com/scim/:uid/Users
```

#### Parameters

| Parameter| Description |
|----------|-------------|
| **uid** | The UID of your SSO organization. |

The payload must be a JSON document that contains the following parts from the full user representation:

- **userName** The user account name used to identify the user. This must be the same as the Subject's NameID passed by the SAML provider.
- **name** The full name that is split into first and last name. The API concatenates these and stores them in a single field.
    - **givenName** First name.
    - **familyName** Last name.
- **emails** An array of email objects. Only the first one is used. For that:
    - **value** Contains the email address.
- **nickName** (optional) The display name of the user in CA Flowdock. If omitted, a nickname based on name is generated.


#### Example request

```
POST https://api.flowdock.com/scim/123456/Users

Authorization: Bearer 1b54iFrTbKIgP0Fl657cHA
Accept: application/json
Content-Type: application/json
```
```json
{
  "userName": "johndoe",
  "name": {
    "givenName": "John",
    "familyName": "Doe"
  },
  "emails": [
    {
      "value":  "john.doe@example.com"
    }
  ],
  "nickName":"JohnDoe"
}

```

#### Response

A successfull request will result in a `201 Created` response.

```json
{
  "id": 1,
  "externalId":"johndoe",
  "userName":"johndoe",
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
  "nickName":"JohnDoe"
}
```

Alternatively, a validation error will result in a `400 Bad request` response. If a user by the `userName` or `email` already exists, a `409 Conflict` response will be returned.
