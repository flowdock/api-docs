Flowdock APIs return errors in JSON format with message and detailed information about the errors.

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