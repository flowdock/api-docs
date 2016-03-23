# REST API

Use the REST API to authenticate as a user, get information about flows and post messages.

* [Authentication](authentication)
* [Flow resource](flows)
* [Message resource](messages)
* [Private conversation resource](private-conversations)
* [Private message resource](private-messages)
* [User resource](users)
* [Organization resource](organizations)
* [Source resource](sources) (for team inbox messages)
* [Invitation resource](invitations)
* [Files](files)

## Structure of an API request

### HTTP Method
We try to uphold RESTful principles in the API. Listing, showing and other non-state-altering actions are done with _GET_, creating with _POST_ and deleting with _DELETE_.

_PATCH_ is used for most update methods instead of _PUT_ because of _PUT_'s inefficiency and inconvenient requirements of idempotency.

<div id="/url-breakdown"></div>
### URL Breakdown
```
https://user:pass@api.flowdock.com/flows/:org/:flow/messages/:message_id
```

* `https`: The CA Flowdock API is served over secure HTTP *only*.
* `user:pass`: HTTP basic authentication credentials. You can use a user's email/password or user-specific tokens. See [Authentication](authentication). **NOTE**: When using email/password, remember to URI encode them. Characters outside the ASCII character set can exist in the e-mail address (the @ sign) and password.
* `api.flowdock.com`: The API endpoint domain.
* `flows`: The resource which is being requested.
* `org`: The `parameterized_name` of the [organization](organizations).
* `flow`: The `parameterized_name` of the [flow](flows).
* `messages`: The sub-resource, a resource which is accessed in the scope of the parent resource. In some cases there may be multiple sub-resources.
* `message_id`: Identifier of the sub-resource.

### Body
Both JSON and HTTP POST data is accepted as request body formats. In the API documentation, JSON examples are used.

Example of posting a message:

```
POST /flows/organization/main/messages
```

```json
{
  "event": "message",
  "content": "Hi all!"
}
```

Response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```

```json
{}
```

### Headers

Content-Type must be defined in the header of requests that contain data.

```
Content-Type: application/json
```
or

```
Content-Type: application/x-www-form-urlencoded
```

The Accept header should be set to `application/json` since that is the supported return format of the API.

```
Accept: application/json
```

## Structure of an API response

### Response body
The API always returns JSON.

Example:

```
GET /flows/some_organization
```

Response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```

```json
[
  {
    "id": "e2366fd7-d720-4272-b32c-3dc995fc549d",
    "name": "My flow",
    "organization": "Acme",
    "unread_mentions": 0,
    "url": "https://api.flowdock.com/flows/acme/my-flow",
    "web_url": "https://acme.flowdock.com/flows/my-flow"
  },
  {
    "id": "26dd3da8-4d6d-42a0-8032-c22fc55907b0",
    "name": "Another flow",
    "organization": "Acme",
    "unread_mentions": 0,
    "url": "https://api.flowdock.com/flows/acme/another-flow",
    "web_url": "https://acme.flowdock.com/flows/another-flow"
  },
]
```

### Response headers

In addition to standard headers like `Content-Type`, the response headers also include the user id of the authenticated user in the `Flowdock-User` header (when available).

```
Status: 200
Content-Length: 27
Content-Type: application/json; charset=utf-8
Flowdock-User: 1
```
