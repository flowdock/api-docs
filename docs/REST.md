# REST API

Authenticate as a user. Get information about flows and post messages as a Flowdock user.

* [Authentication](Authentication)
* [Flow resource](Flows)
* [Message resource](Messages)
* [Private conversation resource](Private Conversations)
* [Private message resource](Private Messages)
* [User resource](Users)
* [Organization resource](Organizations)
* [Source resource](Sources) (for team inbox messages)
* [Invitation resource](Invitations)
* [Files](Files)

## Structure of an API request

### HTTP Method
We try to uphold the principles of REST in the API. Listing, showing and anything else non-state-altering is done with _GET_, creating with _POST_, deleting with _DELETE_.

_PATCH_ is used for most update methods instead of _PUT_ because of its inefficiency and inconvenient requirements of idempotency.

<div id="/url-breakdown"></div>
### URL Breakdown
```
https://user:pass@api.flowdock.com/flows/:org/:flow/messages/:message_id
```

* `https` -- all the Flowdock APIs are served with secure HTTP *only*
* `user:pass` -- HTTP basic authentication credentials. You can use email/password or user specific tokens. See [Authentication](Authentication). **NOTE**: When using email/pass, remember to URI encode them. Characters outside the ASCII charset can exist in the e-mail address (the @ sign) and password.
* `api.flowdock.com` -- the API endpoint domain
* `flows` -- the resource which is being requested
* `org` -- the `parameterized_name` of [organization](Organizations)
* `flow` -- the `parameterized_name` of [flow](Flows)
* `messages` -- sub-resource, a resource which is accessed in the scope of the parent resource. Eg. all the comments of a Team Inbox item. In some cases there can be multiple sub-resources.
* `message_id` -- identifier of the sub-resource

### Body
Both JSON and HTTP POST data is accepted as request body formats. In the API documentation, JSON examples are used.

Example of posting a message:

```
POST /flows/organization/main/messages
```

```
{
  "event": "message",
  "content: "Hi all!"
}
```

Response:

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{}
```

### Headers

Content-Type must be defined with a header in requests that contain data.

```
Content-Type: application/json
```
or

```
Content-Type: application/x-www-form-urlencoded
```

Accept header should include `application/json` since that is the supported return format of the API

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
```
[
  {
    "id": "acme/my-flow",
    "name": "My flow",
    "organization": "Acme",
    "unread_mentions": 0,
    "url": "https://api.flowdock.com/flows/acme/my-flow",
    "web_url": "https://acme.flowdock.com/flows/my-flow"
  },
  {
    "id": "acme/another-flow",
    "name": "Another flow",
    "organization": "Acme",
    "unread_mentions": 0,
    "url": "https://api.flowdock.com/flows/acme/another-flow",
    "web_url": "https://acme.flowdock.com/flows/another-flow"
  },
]
```

### Response headers

In addition to standard headers like `Content-Type`, the response headers also include the user id of the authenticated user in `Flowdock-User` header, when available.

```
Status: 200
Content-Length: 27
Content-Type: application/json; charset=utf-8
Flowdock-User: 1
```
