# Sources

Sources provide content for a flow's team inbox. RSS feeds or Twitter keyword searches are examples of sources.

<div id="/list"></div>
## List Sources

Lists all the sources of the given flow.

```
GET /flows/:organization/:flow/sources
```
[URL breakdown](rest#/url-breakdown)

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
[
  {
    "id": "twitter_keyword-3",
    "type": "twitter_keyword",
    "config": {
        "param": "mysearch",
        "replies": false,
        "retweets": true
    },
    "url": "https://api.flowdock.com/flows/acme/my-flow/sources/twitter_keyword-3"
  },
,
  {
    "id": "feed-1",
    "type": "feed",
    "config": {
      "url": "http://blog.flowdock.com/feed/",
      "title": "Flowdock Blog"
    },
    "url":"https://api.flowdock.com/flows/acme/my-flow/sources/feed-1"
  }
]
```

| Name          | Description  |
| ------------- | ------------ |
| id | Source resource ID. |
| type | Type of the source. Allowed types: `twitter_keyword`, `twitter_user` and `feed`. |
| url | Source resource URL. |
| config | Configuration data of the source. See below for more information about the contents. |

<div id="/get"></div>
## Get a Source
```
GET /flows/:organization/:flow/source/:id
```
[URL breakdown](rest#/url-breakdown)

Get a single source. The data format is identical to the list above.

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
```
{
  "id": "feed-1",
  "type": "feed",
  "config": {
    "url": "http://blog.flowdock.com/feed/",
    "title": "Flowdock Blog"
  },
  "url":"https://api.flowdock.com/flows/acme/my-flow/sources/feed-1"
}
```

<div id="/create"></div>
## Create a Source
```
POST /flows/:organization/:flow/sources
```
[URL breakdown](rest#/url-breakdown)

Create a source for the specified flow.

### Parameters

| Name          | Description  |
| ------------- | ------------ |
| type | Type of the source. Allowed types: `twitter_keyword`, `twitter_user` and `feed`. |
| config | Configuration data for the source. The required content depends on the selected `type`. See below for details per type. |

**Feed** &ndash; An RSS / Atom feed

| Name          | Description  |
| ------------- | ------------ |
| url | **Required** The URL of the feed. |
| title | The title of the feed. |

**Twitter follow** &ndash; Matches tweets that are from a particular Twitter user

| Name          | Description  |
| ------------- | ------------ |
| twitter\_user\_id | **Required** Numeric identifier of the user in Twitter as a string. |
| param | **Required** Twitter username of the user. Must NOT include the `@` prefix. Example: `jdoe` |
| name | **Required** Display name of the user. Example: `John Doe` |
| replies | **Required** Should the search return tweets that are replies to another tweet, ie. have `in_reply_to` field set in Twitter? `true` or `false` |
| retweets | **Required** Should retweets be included? When set to true, each retweet of the user's tweets will cause a new message to appear in team inbox. `true` or `false` |

**Twitter keyword** &ndash; Matches tweets that contain given keywords.

| Name          | Description  |
| ------------- | ------------ |
| param | **Required** The keyword(s) for search. A simple AND search is performed with the given keywords. Phrase searches are not supported. |
| replies | **Required** Should the search return tweets that are replies to another tweet, ie. have `in_reply_to` field set in Twitter? `true` or `false` |
| retweets | **Required** Should retweets be included? When set to true, each retweet of a matching tweet will cause a new message to appear in team inbox. `true` or `false` |

_Example_

```javascript
{
  "type": "twitter_keyword",
  "config": {
    "param": "flowdock",
    "replies": true,
    "retweets": false
  }
}
```

### Response
```
HTTP/1.1 201 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 9
```
```
{
  "id": "twitter_keyword-5",
  "type": "twitter_keyword",
  "config": {
      "param": "flowdock",
      "replies": true,
      "retweets": false
  },
  "url": "https://api.flowdock.com/flows/acme/my-flow/sources/twitter_keyword-5"
},
```

<div id="/delete"></div>
## Delete a Source
```
DELETE /flows/:organization/:flow/source/:id
```
[URL breakdown](rest#/url-breakdown)

Delete a source with the specified id.

### Response
```
HTTP/1.1 204 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
