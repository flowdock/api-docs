# Tags
In Flowdock, the user interacts directly with two types of tags: *hashtags* and *usertags*. Hashtags are prefixed with a hash (#) and usertags with an at sign (@). All tags are case insensitive. Hashtags are stored as-is and usertags are translated into internal meta tags, which are prefixed with colons (:).

[Messages](messages) can be tagged both by referring to tags in the content and explicitly defining tags by using the `tags` property. The meta tags are added based on intrinsic rules.

The following message sent to Flowdock...

```
{
  "event": "message",
  "content": "@Marty, there's a severe #bug in the flux-capacitor, see http://example.com",
  "tags": ["#space-time-continuum"]
}
```

...would be dispatched to other listening clients in this form:

```
{
  "event": "message",
  "content": "@Marty, there's a severe #bug in the flux-capacitor.",
  "tags": [":user:16", ":unread:16", ":url", "bug", "space-time-continuum"]
}
```

For a comprehensive ruleset and a reference implementation of how tags should be parsed in Flowdock, see [Flowdock-text](https://www.github.com/flowdock/flowdock-text), a library that provides utilities for tag extraction and manipulation. It also contains the master set of tests for tag parsing in Flowdock.

## Hashtags
A hashtag is almost any string prefixed with a hash sign. The following are all valid hashtags:

```
#test
#test-run
#test_run
#16
#2test-runs4
```

## Usertags
Tags beginning with an at-sign get mapped to the id of a user with corresponding nick. Disabled users are ignored. Usertagging a message generally causes a few user-specific meta tags to be added to the message.

### User

If a message gets tagged with, for example, `@Marty`, the meta tag that is actually added is `:user:16` (or whatever Marty's user id is). This mapping allows for users to change their nicknames and not lose reference to messages that were tagged earlier. The raw form should not be shown to user, but the human-readable tag `@Marty` should be used instead. The list of a flow's users can be fetched using the [Flows resource](Flows).

Example:

```
:user:16
```
### Unread

If a message body mentions a user's nick or the message gets tagged with either the user's nick or `@everyone`, a meta tag `:unread:{user.id}` is added for the relevant user(s). It is the client's duty to discard this tag when the user sees the message (or with a suitable "mark message as read" mechanism).

Example:

```
:unread:16
```

### Highlight

When a message mentions a user, it should be highligted for them. If a message contains a nick of a user without the at-character, it gets tagged with `:highlight:id`.

Example:

```
:highlight:16
```

### Everyone

This tag is used to get everyone's attention. `:user:everyone` is added to any message which is tagged using one of `@everyone`, `@everybody`, `@all`, `@anyone`, `@anybody`. This tag is usually added in conjunction with `:unread:{user.id}` tags.


## Meta tags

### Links

If a message body contains a URL, it gets tagged with `:url`. This tag can be used to filter all messages that contain links.

### Files

If a message has file attachments, it gets tagged with `:file`. This can be expected for file uploads and emails that contain attachments.

### Comments
> DISCLAIMER: This tag type is likely to change in the near future.

[Team Inbox comments](Message-Types) get a tag of the form `influx:3456` to store a reference to the id of the (parent) commented Inbox item.

Example:

```
influx:123445
```


### Team inbox source

All messages sent to the Team Inbox via [Team Inbox Push API](Team-Inbox) get tagged with a user-provided `source` parameter.

Example:

```
:source:my-fancy-source
```
