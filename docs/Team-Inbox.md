# Push API: Team Inbox (deprecated)

**Deprecated, use the [Messages endpoint](Messages) instead.**

Send mail-like messages to the team inbox of a flow.

```
POST https://api.flowdock.com/v1/messages/team_inbox/:flow_api_token
```

## Parameters

| Name          | Description  |
| ------------- | ------------ |
| source | **Required** Human readable identifier of the application that uses the Flowdock Push API. Only alphanumeric characters, underscores and whitespace can be used. This identifier will be used as the primary method of categorization for the messages. Example value: `Awesome Issue Management App` |
| from_address | **Required** Email address of the message sender. The email address is used to show a avatar of the sender. You can customize the avatar by registering the address in Gravatar. Example value: `john.doe@yourdomain.com` |
| subject | **Required** Subject line of the message. It will be displayed as the title of the team inbox message. |
| content | **Required** Content of the message. It will be displayed as the body of the team inbox message. The following HTML tags can be used in the content: `a abbr acronym address article aside b big blockquote br caption cite code dd del details dfn div dl dt em figcaption figure footer h1 h2 h3 h4 h5 h6 header hgroup hr i img ins kbd li nav ol p pre samp section small span strong sub summary sup table tbody td tfoot th thead tr tt ul var wb`|
| from_name | Name of the message sender. Example value: `John Doe` |
| reply_to | Email address for replies. It will be used when replying to the message from Flowdock.  |
| project | Human readable identifier for more detailed message categorization. Only alphanumeric characters, underscores and whitespace can be used. This identifier will be used as the secondary method of categorization for the messages. Example value: `My Project` |
| format | Format of the message content. The default value is `html`. Only HTML is currently supported. |
| tags | List of [tags](Tags) to be added to the message. Can either be an array (JSON only) or a string with tags delimited with commas. User tags should start with '@'. Hashtags can optionally be prefixed with "#". Tags are case insensitive. These are equivalent: `["@Mike", "#cool", "awesome"]` and `"#awesome,cool,@mike"` |
| link | Link associated with the message. This will be used to link the message subject in the team inbox. Example value: `http://www.flowdock.com/` |

```
{
  "source": "News digest service",
  "from_address": "news@example.com",
  "subject": "Daily digest",
  "content": "Interesting news of today",
  "tags":  ["news", "#digest", "@all"]
}
```

## Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```
```
{}
```

## UI Legend
![Team inbox message with fields described](https://raw.github.com/flowdock/api-docs/master/images/flowdock-api-team-inbox.png)

1. `from_name` and/or `from_address`
2. `source`
3. `project`
4. `subject` (`link` used as URL)
5. `content`
6. `tags`
