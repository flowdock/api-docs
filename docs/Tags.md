#Tags
In Flowdock the user interacts directly with two types of tags, *hashtags* and *usertags*. Hashtags are prefixed with a hash (#) and usertags with an at sign (@). All tags are case insensitive. Hashtags are stored as-is and usertags are translated into internal meta tags, which are prefixed with colons (:).

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

For a comprehensive ruleset and a reference implementation on how tags should be parsed in Flowdock, see [Flowdock-text](https://www.github.com/flowdock/flowdock-text), a library that provides utilities for tag extraction and manipulation. It also contains the master set of tests for tag parsing in Flowdock.

##Hashtags
A hashtag is almost any string prefixed with a hash sign. The following are all valid hashtags:

```
#test
#test-run
#test_run
#16
#2test-runs4
```

##Usertags
Tags beginning with an at-sign get mapped to the id of a user with corresponding nick. Disabled users are ignored. Usertagging a message generally causes a few user-specific meta tags to be added to the message.

###User

If a message gets tagged with `@Marty` for example, the meta tag actually added is `:user:16`, or whatever Marty's user id is. This mapping allows for users to change their nicknames and not lose reference to messages tagged earlier. The raw form should not be shown to user, but the human-readable tag `@marty` should be used instead. List of flow users can be fetched using the [Flows resource](Flows).

####Example

```
:user:16
```
### Unread

If a message body mentions a user's nick or it gets tagged either with `@{user.nick}` or `@everyone`, a meta tag `:unread:{user.id}` is added for the relevant user(s). It remains the client's duty to either discard this tag or to find out a suitable mechanism to "mark the message as read".

####Example

```
:unread:16
```

### Highlight

When a message mentions the user it should be highligted to her. If a message contains a nick of a user without the at-character, it gets tagged with `:highlight:id`.

####Example

```
:highlight:16
```

### Everyone

This tag is used to get everyone's attention. `:user:everyone` is added to any message which is tagged using one of `@everyone`, `@everybody`, `@all`, `@anyone`, `@anybody`. Comes usually in conjunction with `:unread:{user.id}` tags.

##Meta tags
###Links

If a message body contains a URL, it gets tagged with `:url`. This tag can be used to filter all messages containing links.


###Files

If the message has files attached, it gets tagged with `:file`. This can be expected especially in file uploads and emails containing attachments.

###Comments
> DISCLAIMER: This tag type is likely to change in the near future.

[Team Inbox comments](Message-Types) get a tag of the form `influx:3456` to store the reference to the commented Inbox item.

####Example

```
:influx:123445
```


###Team inbox source

All messages sent to Team Inbox via [Team Inbox Push API](Team-Inbox) get tagged with user-provided `source` parameter.

####Example

```
:source:my-fancy-source
```


