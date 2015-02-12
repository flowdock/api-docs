# Push API: Chat (deprecated)

**Deprecated, use the [Messages endpoint](Messages) instead.**

Post messages to a flow's chat from an "external user".

```
POST https://api.flowdock.com/v1/messages/chat/:flow_api_token
```

## Parameters

| Name          | Description  |
| ------------- | ------------ |
| content | **Required** Content of the message. Tags will be automatically parsed from the message content. Maximum length: 8096 characters. |
| external\_user\_name | **Required** Name of the "user" sending the message. |
| tags | Tags of the message, separated by commas. Example value: `cool,stuff` |
| message_id | id of another message that's being commented. If supplied, instead of a regular message, adds a comment to another message. Only messages that are not comments themselves can be commented. |
```
{
  "content": "Howdy-Doo @Jackie #awesome",
  "external_user_name": "Stevie",
  "tags":  ["todo", "#feedback", "@all"]
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
