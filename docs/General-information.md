# General Information
## Cross-Origin resource sharing (CORS)
All the Flowdock APIs support CORS and preflight OPTIONS requests.

The following headers are set for each API response:

```
Access-Control-Allow-Origin: *
Access-Control-Allow-Methods: POST, PUT, PATCH, DELETE, GET, OPTIONS
Access-Control-Allow-Headers: Origin, Accept, Content-type, Authorization, X-CSRF-Token, X-Requested-With, X-Prototype-Version
```

## All requests must be sent via HTTPS

All the Flowdock APIs are served under HTTPS only.

## Content-Type header must always be specified

The Content-Type header of requests should always be specified when posting to the API. Generally, two content-types are supported: `application/json` and `application/x-www-form-urlencoded` (HTTP POST data).

## Responses are JSON documents

The responses of the Flowdock APIs are in JSON format. In case of an error, the JSON response contains a brief message and detailed information about the errors.

Example:

```
POST https://api.flowdock.com/v1/messages/chat/_YOUR_API_TOKEN_HERE_
```
```
{
  "external_user_name": "foobar",
  "content": ""
}

```

Response:

```
HTTP/1.1 400 Bad Request
Content-Type: application/json; charset=utf-8
```
```
{
  "message":  "Validation error",
  "errors":  {
    "content": ["can't be blank"]
  }
}
```
