# Invitations

Invitations API can be used to invite new [users](Users) to [flows](Flows).

## List invitations
Lists open (not yet accepted) invitations of a flow.

```
GET /flows/:organization/:flow/invitations
```
[URL breakdown](rest#/url-breakdown)

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
[
  {
    "id": 14,
    "state": "pending",
    "email": "person@example.com",
    "flow": "05654aa3-3b13-4a79-8cea-92ed45bdc9a5",
    "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/14"
  },
  {
    "id": 15,
    "state": "pending",
    "email": "otherperson@example.com",
    "flow": "05654aa3-3b13-4a79-8cea-92ed45bdc9a5",
    "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/15"
  }
]
```

* `id`: Invitation resource id
* `state`: State of the invitation. Can be `pending` or `accepted`. Invitation can be declined by deleting it.
* `email`: Email address of the person who received the invitation.
* `flow`: Resource id of the flow that this invitation belongs to.
* `url`: Invitation resource url.

## Get an invitation

Get a single invitation. Shows accepted invitations too. Data format and fields are the same as in invitations listing.

```
GET /flows/:organization/:flow/invitations/:id
```
[URL breakdown](rest#/url-breakdown)

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  "id": 14,
  "state": "pending",
  "email": "person@example.com",
  "flow": "05654aa3-3b13-4a79-8cea-92ed45bdc9a5",
  "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/14"
}
```

## Create invitation

Can be used to create or resend invitation. To resend an invitation, just post the same email address that was originally used.

```
POST /flows/:organization/:flow/invitations
```
[URL breakdown](rest#/url-breakdown)

### Input

* `email`: An email address to invite.
* `message`: (optional) Message to add to the invitation.

```javascript
{
  "email": "someone@example.com",
  "message": "Please join our team's Flow."
}
```

### Response
```
HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  "id": 9,
  "state": "pending",
  "email": "person@example.com",
  "flow": "05654aa3-3b13-4a79-8cea-92ed45bdc9a5",
  "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/9"
}
```

## Import address list

The resource can be used to import list of email addresses and invite them to the flow. If an email address matches to existing user in the organization, the user will be added to the flow immediately.

```
POST /flows/:organization/:flow/invitations/import
```
[URL breakdown](rest#/url-breakdown)

### Input

* `list`: A list of email addresses to invite. The list can be separated by commas or linefeeds, and the email addresses can be in either of the following formats: `person@example.com` or `"Joe Smith" <person@example.com>`
* `message`: (optional) Message to add to the invitation.

```javascript
{
  "list": "someone@example.com, \"Joe Smith\" <person@example.com>",
  "message": "Please join our team's Flow."
}
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  "invitations": [
    {
      "id": 9,
      "state": "pending",
      "email": "person@example.com",
      "flow": "acme:my-flow",
      "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/9"
    }
  ],
  "added_users": [
    {
      "id": 1,
      "nick": "Steve",
      "name": "Steve",
      "email": "tide@nodeta.fi",
      "avatar": "https://d1xgiuhrdvh29i.cloudfront.net/avatars/17e5d2cd009f410f75e9eb39f1b54d1c/",
      "status": null,
      "disabled": false,
      "last_activity": 1351230217794,
      "last_ping": 1351230217800
    }
  ],
  "erroneous_emails": [
    {"test@example.invalid" => "Invalid email"}
  ]
}
```

* `invitations`: An array of the created invitations.
* `added_users`: An array of the users added to the flow. E.g. those users who already were in the same organization and in the imported email address list.
* `erroneous_emails`: An array of problematic email addresses with detailed error descriptions.


## Delete invitation

Deletes a single invitation. Only pending invitations can be deleted. Used to decline or cancel an invitation.

```
DELETE /flows/:organization/:flow/invitations/:id
```
[URL breakdown](rest#/url-breakdown)

### Response
```
HTTP/1.1 204 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{}
```
