# Push API

Use the Push API to post content to 

* [Team Inbox](Team Inbox)
* [Chat](Chat)

## Authentication

The Push API does not require authenticating as a user. In order to access the information of a flow you must know its API Token. To obtain the API Token go to **Settings -> Team Inbox** inside a flow or see [Tokens](/account/tokens) page for all your tokens. When accessing the API Resources this token needs to be placed into the request URL.

## Content-Type

**The Content-Type header must always be specified.** The Push API endpoints support both `application/json` and `x-www-urlencoded`.