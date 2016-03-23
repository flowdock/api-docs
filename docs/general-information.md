# General Information

## Cross-origin resource sharing (CORS)
All the CA Flowdock APIs support CORS and preflight OPTIONS requests. Provided that the request has set the required `Origin` header, the resulting response will have a matching `Access-Control-Allow-Origin` header along with other CORS headers:

```
Access-Control-Allow-Origin: example.com
Access-Control-Allow-Methods: POST, PUT, PATCH, DELETE, GET, OPTIONS
Access-Control-Allow-Headers: Origin, Accept, Content-type,
Authorization, X-CSRF-Token, X-Requested-With, X-Prototype-Version
```

If the REST API request fails, make sure that you are authorized with either a valid OAuth 2.0 access token or by providing the [HTTP Basic authentication](http://en.wikipedia.org/wiki/Basic_access_authentication) credentials in the request header. Some client libraries won't send the credentials if they are set in the URL. See the [Authentication page(authentication) for authentication examples.

## All requests must be sent via HTTPS

All the CA Flowdock APIs are served under HTTPS only.

## Content-Type header must always be specified

The Content-Type header of requests should always be specified when posting to the API. Generally, two content-types are supported: `application/json` and `application/x-www-form-urlencoded` (HTTP POST data).

## Responses are JSON documents

The responses of the CA Flowdock APIs are sent using the JSON format. In case of an error, the JSON response contains a brief message and detailed information about the error.

Example:

```
POST https://api.flowdock.com/v1/messages/chat/_YOUR_API_TOKEN_HERE_
```

```json
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
```json
{
  "message":  "Validation error",
  "errors":  {
    "content": ["can't be blank"]
  }
}
```
