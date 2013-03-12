# Authentication

The REST API uses HTTP Basic Auth.

## Authenticating as a user
The entire REST API requires authenticating as a Flowdock user. All actions are performed as that user, with the ACL of that user. The REST API resources are always accessed in the context of the authenticated user, so the same ACL limitations apply.
Flowdock APIs require HTTP Basic Authentication in one of two ways.

### Basic Auth: User tokens

The preferred method of authentication is using user tokens. You can generate such a token at [Tokens](/account/tokens) page. Use the token simply as the username with Basic Auth. The password can be anything.  Thereby the following authentication attempts are equivalent:

```
https://deadbeefdeadbeef@api.flowdock.com/flows/acme/main/messages
https://deadbeefdeadbeef:DUMMY@api.flowdock.com/flows/acme/main/messages
```

### Basic Auth: email / pass
Basic Auth works using an actual Flowdock user's username and password as credentials. NOTE: depending on the context where you are using the API, you might have to take care of properly URL encoding the username and password.

## Anonymous posting
Some messages can be posted to Flowdock flows simply using the API token of the flow. See the [Push API](Push).

## Usage

Some client libraries won't send the credentials if they are set in the URL. This means that the credentials should be provided with [HTTP Basic authentication](http://en.wikipedia.org/wiki/Basic_access_authentication) request header instead:

```
Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==
```

Or with jQuery:

```
$.ajax({
  type: 'GET',
  url: 'https://api.flowdock.com/flows',
  beforeSend: function (xhr) {
    xhr.setRequestHeader('Authorization', 'Basic ' + btoa('email:password'));
  },
  success: function(data) {
    console.log(data);
  }
});
```