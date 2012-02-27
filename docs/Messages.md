# Messages

The Message resource represents all the different messages in Flowdock. Message is a sub-resource for Flow, so it should always be accessed in that context: `/flows/organization/flow_name/messages`.

## Sending a message to a flow

```
POST /flows/:organization/:flow/messages
```

### Input
* `event`
    One of the valid Flowdock message events. Determines the type of message being sent to Flowdock. See Message Types section below. Required.
* `content`
    The actual message. The format of content depends on the event. See Message Types section below. Required.
* `tags`
List of tags to be added to the message. Can be either an array (JSON only) or a string with tags delimited with commas. User tags should start with '@'. Hashtags can optionally be prefixed with "#". Tags are case insensitive. These are equivalent:
  * `["@Mike", "#cool", "awesome"]`
  * `"#awesome,cool,@mike"`

```javascript
{
  "event": "message",
  "content": "Howdy-Doo @Jackie #awesome",
  "tags":  ["todo", "#feedback", "@all"]
}
```

### Response
```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```
```
{}
```

## Message Types
The event parameter in the message data defines the type of message.

### Normal Chat message
_Event: `message`_

For chat messages, the content is simply a string which represents the chat messages content. Tags are parsed from the message.

### Status update
_Event: `status`_

Format is the same as with `message`. Sets the status of the user.

### Team Inbox message
_Event: `mail`_

Format is the similar to `message`. For required format, see [Push API: Team Inbox](Team Inbox).
