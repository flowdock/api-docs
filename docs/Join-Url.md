# Join Url

Join Url api can be used to control if a [flow](Flow) can be joined with a shared link or not.

## Get a Join Url

Returns the flow's join url if it exists.

```
GET /flows/:organization/:flow/join_url
```

### Response

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  join_url: "https://acme.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow"
}
```

* `join_url`: Url that can be used to join the flow or nil if it is not enabled.

## Create Join Url

Enables join url for flow.

```
POST /flows/:organization/:flow/join_url
```

### Response
```
HTTP/1.1 201 Created
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  join_url: "https://acme.flowdock.com/invitations/eedd2bf0643f75c14be9099272429351c7132a71-my-flow"
}
```

## Destroy Join Url

Disables join url for flow.

```
DELETE /flows/:organization/:flow/join_url
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
