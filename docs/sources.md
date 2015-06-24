# Sources

Sources provide content from external sources to a flow. These include
[integrations](/integrations) such as GitHub, Rally and Zendesk. Each source has a secret
`flow_token` that is used for authentication when [posting content to a flow](production-integrations#/post-inbox).

Sources can be accessed with [flow token authentication](authentication#source-token),
in which case only the specific source is exposed. Creating new sources requires
OAuth 2.0 authorization by an end user. See [the exhaustive integration guide](production-integrations) for more information on building integrations that use the OAuth framework.

If you just want to post messages to your own flows, have a look at the [the quick integration guide](integration-getting-started) for instructions on how to create sources without the OAuth authorization process.


<a id="/list"></a>
## List Sources

Lists all sources visible for user.

```
GET /sources
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
    "id": 1,
    "name": "Example",
    "url": "https://api.flowdock.com/flows/example/main/sources/1",
    "created_at": "2014-10-14T11:05:27.176Z",
    "updated_at": "2014-10-14T11:05:27.176Z",
    "_links": {},
    "application": {
      "id": 327,
      "name": "Zendesk",
      "icon_url": "https://dxgv4vuja9avs.cloudfront.net/applications/327/64981f9198413209.png",
      "url": "http://api.flowdock.com/oauth/applications/327"
    },
    "creator": {
      "id": 1,
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c",
      "email": "testuser@example.com",
      "name": "User",
      "nick": "User",
      "website": "http://www.example.com/"
    }
  },
  {
    "id": 2,
    "name": "Engineering",
    "url": "https://api.flowdock.com/flows/example/other/sources/2",
    "external_url": "https://acme.example.com/polls/helsinki",
    "created_at": "2014-10-14T11:05:27.007Z",
    "updated_at": "2014-10-14T11:05:27.007Z",
    "_links": {},
    "application": {
      "id": 327,
      "name": "Zendesk",
      "icon_url": "https://dxgv4vuja9avs.cloudfront.net/applications/327/64981f9198413209.png",
      "url": "http://api.flowdock.com/oauth/applications/327"
    },
    "creator": {
      "id": 1,
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c",
      "email": "testuser@example.com",
      "name": "User",
      "nick": "User",
      "website": "http://www.example.com/"
    }
  }
]
```

<a id="/list-flow"></a>
## List Flow Sources

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

```json
[
  {
    "id": 1,
    "name": "Example",
    "url": "https://api.flowdock.com/flows/example/main/sources/1",
    "created_at": "2014-10-14T11:05:27.176Z",
    "updated_at": "2014-10-14T11:05:27.176Z",
    "_links": {},
    "application": {
      "id": 327,
      "name": "Zendesk",
      "icon_url": "https://dxgv4vuja9avs.cloudfront.net/applications/327/64981f9198413209.png",
      "url": "http://api.flowdock.com/oauth/applications/327"
    },
    "creator": {
      "id": 1,
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c",
      "email": "testuser@example.com",
      "name": "User",
      "nick": "User",
      "website": "http://www.example.com/"
    }
  },
  {
    "id": 2,
    "name": "Engineering",
    "url": "https://api.flowdock.com/flows/example/main/sources/2",
    "external_url": "https://acme.example.com/polls/helsinki",
    "created_at": "2014-10-14T11:05:27.007Z",
    "updated_at": "2014-10-14T11:05:27.007Z",
    "_links": {},
    "application": {
      "id": 327,
      "name": "Zendesk",
      "icon_url": "https://dxgv4vuja9avs.cloudfront.net/applications/327/64981f9198413209.png",
      "url": "http://api.flowdock.com/oauth/applications/327"
    },
    "creator": {
      "id": 1,
      "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c",
      "email": "testuser@example.com",
      "name": "User",
      "nick": "User",
      "website": "http://www.example.com/"
    }
  }
]
```

<div id="/get"></div>
## Get a Source

```
GET /flows/:organization/:flow/source/:id
```

Get a single source.

### Response

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```

```json
{
  "id": 2,
  "name": "Engineering",
  "url": "https://api.flowdock.com/flows/example/main/sources/2",
  "external_url": "https://acme.example.com/polls/helsinki"
  "created_at": "2014-10-14T11:05:27.007Z",
  "updated_at": "2014-10-14T11:05:27.007Z",
  "_links": {},
  "application": {
    "id": 327,
    "name": "Zendesk",
    "icon_url": "https://dxgv4vuja9avs.cloudfront.net/applications/327/64981f9198413209.png",
    "url": "http://api.flowdock.com/oauth/applications/327"
  },
  "creator": {
    "id": 1,
    "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c",
    "email": "testuser@example.com",
    "name": "User",
    "nick": "User",
    "website": "http://www.example.com/"
    }
}
```

<div id="/create"></div>
## Create a Source

```
POST /flows/:organization/:flow/sources
```

Create a source for the specified flow. The source will be assigned a
`flow_token` that can be used to [push content](production-integrations#/post-inbox) by [the OAuth application](production-integrations#/create-app) to the
flow.

### OAuth scope requirements

This endpoint is currently only available for [OAuth applications](production-integrations#/create-app) with scope
`integration`. The OAuth application will be used as the application attribute
of the source.

### Input

| Name          | Description  |
| ------------- | ------------ |
| name          | **Required** The name of the source |
| external_url  | URL to the external resource or entity that this source represents, e.g. GitHub repository |

### Example

```json
{
  "name": "Helsinki Lunch Poll",
  "external_url": "https://acme.example.com/polls/helsinki"
}
```

### Response

```
HTTP/1.1 201 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 9
```

```json
{
  "id": 3,
  "name": "Helsinki Lunch Poll",
  "url": "https://api.flowdock.com/flows/example/main/sources/3",
  "external_url": "https://acme.example.com/polls/helsinki",
  "created_at": "2014-10-14T11:06:28.131Z",
  "updated_at": "2014-10-14T11:06:28.131Z",
  "flow_token": "1b609b5253702a0a7ca3607f02642130",
  "_links": {},
  "application": {
    "id": 1,
    "name": "AwesomeApp",
    "icon_url": "https://dxgv4vuja9avs.cloudfront.net/applications/327/64981f9198413209.png",
    "url": "http://api.flowdock.com/oauth/applications/1"
  },
  "creator": {
    "id": 1,
    "avatar": "https://d2cxspbh1aoie1.cloudfront.net/avatars/f0b4520a6e0001636bf8fc1431af151c",
    "email": "testuser@example.com",
    "name": "User",
    "nick": "User",
    "website": "http://www.example.com/"
  }
}
```

**`flow_token` is only visible in the response of the source creation request.** It cannot be
retrieved later on.

<div id="/update-source"></div>
## Update a Source

After a source has been created, two fields can be updated with a PUT request: an error message and the configuration URL of the source. The error message should be used to indicate a problem with the source, and will be displayed for people in the flow. The configuration URL defines where the user is directed to when clicking the configure link next to the source in the flow's Inbox Settings.

### Using normal authentication methods

```
PUT /flows/:organization/:flow/sources/:id
```

#### Input

| Name                | Description                                                                                |
| --------------------| ------------------------------------------------------------------------------------------ |
| `error_message`     | The error message to be displayed for this source. Clear the error with an empty string    |
| `configuration_url` | The URL where users can configure this source                                              |


### Using the source's `flow_token`

The source can also be updated using its unique `flow_token`. This is not exactly RESTful but is handy when storing the source ID would be unnecessary.

```
PUT /sources
```

#### Input

| Name                | Description                                                                                |
| ------------------- | ------------------------------------------------------------------------------------------ |
| `flow_token`        | The token associated with the source                                                       |
| `error_message`     | The error message to be displayed for this source. Clear the error with an empty string    |
| `configuration_url` | The URL where users can configure this source                                              |

<div id="/delete"></div>
## Delete a Source

```
DELETE /flows/:organization/:flow/source/:id
```

Delete a source with the specified id. This will also invalidate the flow_token
and prevent the source from posting content to the flow.

### Response

```
HTTP/1.1 204 OK
Content-Type: application/json; charset=utf-8
Flowdock-User: 2
```
