# Invitations

Invitations api can be used to invite new users to [flows](Flows). Invitations work for flows with `require_invitation` either true or false.

## List invitations
Lists open invitations to a flow.

```
GET /flows/:organization/:flow/invitations
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
    "id": 14,
    "state": "pending",
    "email": "person@example.com",
    "flow": "acme:my-flow",
    "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/14",
    "created": 1350482765000,
    "updated": 1350483954000
  },
  {
    "id": 15,
    "state": "pending",
    "email": "otherperson@example.com",
    "flow": "acme:my-flow",
    "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/15",
    "created": 1350482865000,
    "updated": 1350483865000
  }
]
```

* `id`: Invitation resource id
* `state`: State of the invitation. Can be `pending` or `accepted`.
* `email`: Email address of the person who received the invitation.
* `flow`: Resource id of the flow that this invitation belongs to.
* `url`: Invitation resource url.
* `created`: Timestamp in milliseconds since epoch when the invitation was created.
* `updated`: Timestamp in milliseconds since epoch when the invitation was resent. Same as created if invitation has not been resent.

## Get an invitation

Get a single invitation. Shows accepted invitations too. Data format and fields are the same as in invitations listing.

```
GET /flows/:organization/:flow/invitations/:id
```

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
  "flow": "acme:my-flow",
  "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/14",
  "created": 1350482765000,
  "updated": 1350483954000
}
```

## Create invitations

Can be used to create or resend invitations. To resend an invitation, just post the same email address that was originally used.

```
POST /flows/:organization/:flow/invitations
```

### Input

* `emails`: An array or comma / whitespace separated list of email addresses to invite.
* `message`: (optional) Message to add to the invitation.

```javascript
{
  "emails": [
    "someone@example.com",
    "invalid"
  ],
  message: "Please join our team's Flow."
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
  "erroneous_emails": [
    "invalid"
  ],
  "invitations": [
    {
      "id": 9,
      "state": "pending",
      "email": "person@example.com",
      "flow": "acme:my-flow",
      "url": "https://api.flowdock.com/flows/acme/my-flow/invitations/9",
      "created": 1351159711000,
      "updated": 1351159711000
    }
  ]
```
* `erroneous_emails`: An array of emails that were invalid. If all of the emails are invalid, the request will fail.
* `invitations`: An array of invitations that were created or resent.


## Delete invitation

Deletes a single invitation. Only pending invitations can be deleted.

```
DELETE /flows/:organization/:flow/invitations/:id
```

### Response
```
HTTP/1.1 204 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{}
```
