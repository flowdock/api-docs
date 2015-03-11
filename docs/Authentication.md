# Authentication

There are three ways to authenticate as a user with the Flowdock API:

1. OAuth 2.0
2. User credentials with HTTP Basic Authentication
3. API token with HTTP Basic Authentication.

Most applications should use OAuth 2.0 as it doesn't store passwords locally and does not require users to have a separate password for Flowdock.

Integrations can use [flow tokens](#source-token) from configured [sources](sources) to POST messages to flows and perform actions on the source.

<div id="/oauth2"></div>
## OAuth 2.0

[OAuth 2.0](http://tools.ietf.org/html/rfc6749) is an authorization framework that enables third-party applications to obtain limited access to Flowdock on the user's behalf without getting their password. This is the preferred authentication method over Basic Authentication because tokens can be limited to specific types of data, and can be revoked by users at any time.

All developers need to [register their application](https://flowdock.com/account/authorized_applications) before getting started. A registered OAuth 2.0 application is assigned a unique Client ID and Client Secret. The Client Secret should not be shared.

Given the security implications of getting the implementation correct, we strongly encourage you to use available OAuth 2.0 libraries.

### Web Application Flow

[Authorization Code Grant](http://tools.ietf.org/html/rfc6749#section-4.1) is probably the most used authorization flow. It allows clients to obtain both access tokens and refresh tokens. The flow is based on redirects and requires the client to have a web browser. Use this flow if your client is another web application.

<div id="/redirect"></div>
#### 1. Redirect users to request Flowdock access

      GET https://api.flowdock.com/oauth/authorize


The following query string parameters can be used:

| Name          | Description  |
| ------------- | ------------ |
| client_id     | **Required** The registered client ID for your application |
| response_type | **Required** Use value `code` |
| redirect_uri  | The URL in your app where users will be sent after authorization. See details below about redirect URLs. |
| scope         | A space-delimited list of scopes. |
| state         | An unguessable random string. This is used to protect authorization consumers (you!) against cross-site request forgery attacks. |

<div id="/redirect-back"></div>
#### 2. Flowdock redirects back to your site

If the user accepts your request, Flowdock redirects back to your site with a temporary authorization code in the redirect URL as well as the state parameter provided in the previous step.

The redirect might look like

    GET https://example.com/oauth/callback?state=123&code=deadbeef&gr

Exchange the `code` for an access token with

    POST https://api.flowdock.com/oauth/token

The following parameters should be present in the request:

| Field          | Description                       |
| -------------- | --------------------------------- |
| client_id      | **Required** The registered client ID for your application. |
| client_secret  | **Required** The client secret for your application. |
| code           | **Required** The authorization code returned from the initial request. |
| redirect_uri   | **Required** The URI registered with the application. |
| grant_type     | **Required** As defined in the OAuth 2.0 specification, this field must contain a value of `authorization_code`. |

A successful response to this contains the following fields:

| Field          | Description                       |
| -------------- | --------------------------------- |
| access_token   | The token that can be used to access the Flowdock API. |
| refresh_token  | A token that may be used to obtain a new access token. This is only present when the `offline_access` scope is used. |
| expires_in     | The remaining lifetime on the access token. |
| token_type     | The type of token received. At this time, this field will always have the value `Bearer`. |

By default, the response will be represented as an `application/x-www-form-urlencoded` string, such as

    access_token=[omitted]&scope=flow%2Cprivate&token_type=bearer

However, you can also specify the JSON content type using the `Accept` header.

    Accept: application/json
    {"access_token":[omitted], "scope":"flow,private", "token_type":"bearer"}

<div id="/make-request"></div>
#### 3. Make API requests with the access token.

The access token allows you to make requests to the API on behalf of a user.

    GET https://api.flowdock.com/user
    Authorization: Bearer OAUTH-TOKEN
    Host: api.flowdock.com

Access tokens can also be used as a query string parameter:

    GET https://api.flowdock.com/user?access_token=...

<div id="/refresh-token"></div>
#### 4. Refresh the access token, if necessary.

OAuth 2.0 access tokens are short-lived. If your application needs access to the Flowdock API beyond the lifetime of a single access token, it can obtain a refresh token. A refresh token allows your application to obtain new access tokens.

Obtain a new access token using the refresh token with

    POST https://api.flowdock.com/oauth/token


| Field          | Description                       |
| -------------- | --------------------------------- |
| refresh_token  | **Required** The last received refresh token. |
| client_id      | **Required** The registered client ID for your application. |
| client_secret  | **Required** The client secret for your application. |
| grant_type     | **Required** As defined in the OAuth 2.0 specification, this field must contain a value of `refresh_token`. |

A successful response will contain a new access token and *may* also contain a new refresh token. After a new refresh is received, the previous refresh token should not be used.

| Field          | Description                       |
| -------------- | --------------------------------- |
| access_token   | The token that can be used to access the Flowdock API. |
| refresh_token  | A token that may be used to obtain a new access token. This is only present when the `offline_access` scope is used. |
| expires_in     | The remaining lifetime on the access token. |
| token_type     | The type of token received. At this time, this field will always have the value `Bearer`. |

The response will by default be represented as `application/x-www-form-urlencoded`, but the `Accept` header can be used to control the response content type.

### Non-Web Application Flow

Applications that do not run in a web browser can use the [Resource Owner Password Credentials](http://tools.ietf.org/html/rfc6749#section-4.3) authentication flow. However, this requires users to type in their Flowdock email address and password.

Use the token interface with email and password from below. With this technique, an email address and password need not be stored permanently, and the user can revoke access at any time.

### Redirect URI

The redirect_uri parameter is optional. If left out, Flowdock will redirect users to the callback URL configured in the OAuth Application settings. If provided, the redirect URL’s host and port must exactly match the callback URL.

If the application is an installed application, the application can use special value `urn:ietf:wg:oauth:2.0:oob` as the redirect URI. This communicates to Flowdock that authorization code should be exchanged out-of-band.

The application can detect when OAuth authentication is completed and extract the authorization code from the page URL. A successful OAuth 2.0 authorization would redirect to

    https://flowdock.com/oauth/authorize/deadbeefdeadbeef

where `deadbeefdeadbeef` is the authorization code.

### Scopes

Scopes are used to specify what API access your application needs. In general, it is good practice to only request the minimum amount of API access necessary for your application.

When using a web-based flow, the scopes will be displayed to the user when authorising the application.

The following scopes are available in the Flowdock API:


| Name           | Description                       |
| -------------- | --------------------------------- |
| (no scope)     | By default, the `flow` and `private` scopes are included. |
| flow           | Read and send messages to flows. |
| private        | Read and send private messages. |
| manage         | Manage flows, organizations and their users. |
| profile        | Access the user's profile. |
| offline_access | Access the API when the user is not present. | 
| integration    | Create [sources](https://www.flowdock.com/api/sources) |


### Requesting tokens

The token endpoint is used to retrieve tokens with different credentials. It can be used with authorization codes, email/password combinations and existing refresh tokens.

    POST /oauth/token

The following parameters are used for the request. `application/x-www-form-urlencoded` and `application/json` content types are accepted for both requests and responses.


| Field          | Description                       |
| -------------- | --------------------------------- |
| client_id      | **Required** The registered client ID for your application |
| client_secret  | **Required** The client secret for your application. |
| grant_type     | **Required** `authorization_code`, `password`, or `refresh_token` depending on the OAuth 2.0 authentication mechanism. |

When using `authorization_code`, the following additional fields are used to authenticate the request

| Field          | Description                       |
| -------------- | --------------------------------- |
| code           | The code returned as part of the redirect from the Flowdock API. |
| redirect_uri   | The URI registered with the application. |

When using `password` grant type, the following additional fields are used to authenticate the request and DO NOT add an Authorization header:

| Field          | Description                       |
| -------------- | --------------------------------- |
| username       | The user's email address.         |
| password       | The user's password.              |
| scope          | A space-delimited list of scopes. |


A successful response will contain the following fields:

| Field          | Description                       |
| -------------- | --------------------------------- |
| access_token   | The token that can be used to access the Flowdock API. |
| refresh_token  | A token that may be used to obtain a new access token. This is only present when the `offline_access` scope is used. |
| expires_in     | The remaining lifetime on the access token. |
| token_type     | The type of token received. At this time, this field will always have the value `Bearer`. |


## HTTP Basic Authentication

[HTTP Basic Authentication](http://tools.ietf.org/html/rfc2617#section-2) allows credentials, such as a username and password, or API tokens to be transferred in HTTP headers. The secret is encoded using [Base64](http://en.wikipedia.org/wiki/Base64).

    Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==

Some clients allow the authentication to be defined as part of the URL.

    $ curl https://deadbeefdeadbeef@api.flowdock.com/flows

### API Tokens

The API tokens are available on the [Tokens](https://flowdock.com/account/tokens) page. These tokens can be simply used as the username with HTTP Basic Authentication. The password is in this case ignored. Thereby, the following authentication attempts are equivalent:

    $ curl https://deadbeefdeadbeef@api.flowdock.com/flows/acme/main/messages
    $ curl https://deadbeefdeadbeef:DUMMY@api.flowdock.com/flows/acme/main/messages

Flowdock has two types of API tokens: user tokens and flow tokens. User API tokens can be used to authenticate as a specific user in [REST API](rest). Flow specific tokens can only be used to authorize anonymous posting to a flow in [Push API](push).

### Email / password

HTTP Basic Authentication also works by using a Flowdock user's username and password as credentials. However, the representation of email addresses in URLs might be problematic.

    $ curl https://email@example.com:password@api.flowdock.com/flows

This can mean that you have to take care of properly encoding the username and password.

As not all users have a separate password for Flowdock (but use Google or Rally as authentication providers), using HTTP Basic Authentication for anything user-facing is strongly discouraged.

<div id="source-token"></div>
## Source Token Authentication

Integrations can POST messages to flows and perform actions on the source using a [source's](sources) `flow_token`. This token is not linked to any user and will exist even if the user who configured the token is removed from the flow.

The `flow_token` should be given via a query string or POST parameters.

    $ curl https://api.flowdock.com/sources?flow_token=deadc0de
