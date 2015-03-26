# Private conversations

A private conversation in Flowdock is like a chat room for only two users. 

Note: the `open` attribute of a closed private conversation automatically changes back to `true` when new unread messages are added to the conversation.

In the REST API, Private conversations are referred to with the recipient's [User](Users) ID.

## List Private conversations

Lists the private conversations of the authenticated user.

```
GET /private
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
[
  {
    "id": 42,
    "url": "https://api.flowdock.com/private/42",
    "name": "@Hubot",
    "open": true,
    "activity": {
      "inbox": false,
      "mentions": 0,
      "chat": false
    },
    "users": [{
      "id": 2,
      "nick": "Antti",
      "email": "antti@flowdock.com",
      "avatar": "https://api.flowdock.com/avatars/667bc62d0ba333c684f0466d7a30a724/",
      "name": "Antti Pitkänen"
    }, {
      "id": 42,
      "nick": "Hubot",
      "email": "hubot@flowdock.com",
      "avatar": "https://api.flowdock.com/avatars/821d585f52de5455c26dca68e9e70e77/",
      "name": "Hubot Hubot"
    }]
  },
  ...
]
```

| Name          | Description  |
| ------------- | ------------ |
| id | Private conversation ID. The same as the recipient's user ID. |
| url | Private conversation resource URL. |
| name | Human-readable name of the conversation. |
| open | Boolean value (true or false). Automatically changes to `true` when new unread messages are posted into the conversation. Clients implementing tabs should display tabs for all the `open` private conversation, and list non-open private conversations elsewhere. |
| activity | Activity information of the private conversation. |
| users | List of conversation participants. |

## Get a Private conversation
```
GET /private/:id
```
Get a private conversation. If no existing conversation between users is found, a new conversation is automatically created with `open` attribute set to `false`. The data format is identical to the list of conversations.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```json
{
  "id": 42,
  "url": "https://api.flowdock.com/private/42",
  "name": "@Hubot",
  "open": true,
  "activity": {
    "inbox": false,
    "mentions": 0,
    "chat": false
  },
  "users": [{
    "id": 2,
    "nick": "Antti",
    "email": "antti@flowdock.com",
    "avatar": "https://api.flowdock.com/avatars/667bc62d0ba333c684f0466d7a30a724/",
    "name": "Antti Pitkänen"
  }, {
    "id": 42,
    "nick": "Hubot",
    "email": "hubot@flowdock.com",
    "avatar": "https://api.flowdock.com/avatars/821d585f52de5455c26dca68e9e70e77/",
    "name": "Hubot Hubot"
  }]
}
```

## Update a Private conversation
```
PUT /private/:id
```
Update private conversation information.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| open | Boolean value (true or false). Controls the visibility of the private conversation. |

```javascript
{
  "open": false
}
```
### Response
```json
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  "id": 42,
  "url": "https://api.flowdock.com/private/42",
  "name": "@Hubot",
  "open": false,
  "activity": {
    "inbox": false,
    "mentions": 0,
    "chat": false
  },
  "users": [{
    "id": 2,
    "nick": "Antti",
    "email": "antti@flowdock.com",
    "avatar": "https://api.flowdock.com/avatars/667bc62d0ba333c684f0466d7a30a724/",
    "name": "Antti Pitkänen"
  }, {
    "id": 42,
    "nick": "Hubot",
    "email": "hubot@flowdock.com",
    "avatar": "https://api.flowdock.com/avatars/821d585f52de5455c26dca68e9e70e77/",
    "name": "Hubot Hubot"
  }]
}
```
