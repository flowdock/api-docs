# Sources

Sources provide information for the team inbox of a flow. For example, RSS feeds or Twitter keyword searches can be sources.

## List Sources

Lists all the sources of the given flow.

```
GET /flows/:organization/:flow/sources
```

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

* `id`: Source resource ID
* `type`: Type of the source, allowed types: `twitter_keyword`, `twitter_user` and `feed`
* `url`: Source resource URL
* `config`: Configuration data of the source. See below for more detail on the contents.

## Get a Source
```
GET /flows/:organization/:flow/source/:id
```
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

## Create a Source
```
POST /flows/:organization/:flow/sources
```
Create a source for the specified flow.

### Input

* `type`: Type of the source, allowed types: `twitter_keyword`, `twitter_user` and `feed`
* `config`: Configuration data for the source, required content depends on the selected `type`. See below for details per type.

_Feed_

* `url`: The URL of the feed.
* `title`: Title of the feed, optional.

_Twitter follow_

Matches tweets that are from a particular Twitter user.

* `twitter_user_id`: Numeric identifier of the user in Twitter. `String`
* `param`: Twitter username of the user. Must NOT include the `@` prefix. Example: `jdoe`
* `name`: Display name of the user. Example: `John Doe`
* `replies`: Should the search return tweets that are replies to another tweet, ie. have `in_reply_to` field set in Twitter? `true` or `false`
* `retweets`: Should retweets be included? When set to true, each retweet of a matching tweet will cause a new message to appear in team inbox. `true` or `false`

_Twitter keyword_

* `param`: The keyword(s) for search. A simple AND search is performed with the given keywords. Phrase searches are not supported.
* `replies`: Should the search return tweets that are replies to another tweet, ie. have `in_reply_to` field set in Twitter? `true` or `false`
* `retweets`: Should retweets be included? When set to true, each retweet of a matching tweet will cause a new message to appear in team inbox. `true` or `false`

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

## Delete a Source
```
DELETE /flows/:organization/:flow/source/:id
```

Delete a source with the specified id.

### Response
```
HTTP/1.1 204 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
