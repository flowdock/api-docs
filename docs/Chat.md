# Push API: Chat

Post messages to chat from an "external user".

```
POST https://api.flowdock.com/v1/messages/chat/:flow_api_token
```

## Input

* `content`
Content of the message. Tags will be automatically parsed from the message content. Required.

* `external_user_name`
Name of the "user" sending the message. Required.

* `tags`
Tags of the message, separated by commas. Optional.
Example value: cool,stuff

```
{
  content: "Howdy-Doo @Jackie #awesome",
  external_user_name: "Stevie",
  tags:	["todo", "#feedback", "@all"]
}
```

## Response
```
{}
```