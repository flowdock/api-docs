## All requests must be sent via HTTPS
All the Flowdock APIs are served under HTTPS only.

## Content Type header must always be specified
The Content-Type header of requests should always be specified when posting to the API. Generally, two content-types are supported: "application/json" and "x-form-urlencoded" (HTTP POST data).

## Responses are JSON documents

The responses of the Flowdock APIs are in JSON format. In case of an error, the JSON response contains a brief message and detailed information about the errors.

Example:

```
// POST https://api.flowdock.com/v1/messages/chat/_YOUR_API_TOKEN_HERE_
{
  "external_user_name": "foobar",
  "content": ""
}

```

Response:

```
// Status: 400
// Content-Type: application/json; charset=utf-8
{
  "message":  "Validation error",
  "errors":  {
    "content": ["can't be blank"]
  }
}
```
