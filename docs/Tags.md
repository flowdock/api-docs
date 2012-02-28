#Tags
In Flowdock the user interacts directly with two types of tags, *hashtags* and *usertags*. Hashtags are prefixed with a hash (#) and usertags with an at sign (@). Messages can also be tagged with a set of special colon-prefixed meta tags that should not be shown to the user. All tags are case insensitive.

[Messages](messages) can be tagged both by referring to tags in the content and explicitly defining tags by using the `tags` property. The different meta tags are added based on intrinsic rules.

The following message sent to Flowdock...

```
{
  event: "message",
  content: "@Marty, there's a severe #bug in the flux-capacitor.",
  tags: ["#space-time-continuum"]
}
```

...would be dispatched to other listening clients in this form:

```
{
  event: "message",
  content: "@Marty, there's a severe #bug in the flux-capacitor.",
  tags: [":user:16", ":unread:16", "bug", "space-time-continuum"],
  ...
}
```

For a comprehensive ruleset and a reference implementation on how tags should be parsed in Flowdock, see [Flowdock-text](https://www.github.com/flowdock/flowdock-text), a library that provides utilities for tag extraction and manipulation. It also contains the master set of tests for tag parsing in Flowdock.

##Hashtags
A hashtag is almost any string prefixed with a hash sign. The following are all valid hashtags and get parsed from a free-form `content` field. 

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

If a message gets tagged with `@Marty` for example, the tag for that message gets turned into `:user:16`, or whatever Marty's user id is. This mapping allows for users to change their nicknames and not lose reference to earilier tagged messages. The raw usertag should not be shown to user, but the human-readable tag should be used instead. List of flow users can be fetched using the [Flows resource](Flows).

####Example

```
:user:16
```
### Unread

If a message body mentions user's nick or it gets tagged either with `@{user.nick}` or `@everyone`, it gets tagged with `:unread:{user.id}` for every user concerned. It remains as the client's duty to either discard this tag or to find out a suitable mechanism to "mark the message as read".

####Example

```
:unread:16
```

### Highlight

The message contains a mention of the user and should be highligted to her. If a message contains a nick of a user without the at-character, it gets tagged with `:highlight:id`. This tag should not be manually removed from the message.

####Example

```
:highlight:16
```

### Everyone

This tag is used to get everyone's attention. `:user:everyone` is added to any message which is tagged using one of these tags `@everyone`, `@everybody`, `@all`, `@anyone`, `@anybody`. Comes usually in conjunction with `:unread:{user.id}` tags.

##Meta tags
###Links

If a message body contains a URL, it gets tagged with `:url`. This tag can be used to filter all messages containing links. This tag should not be manually removed from the message.


###Files

If the message has files attached, it gets tagged with `:file`. This can be expected especially in file uploads and emails containing attachments. This tag should not be manually removed from the message.

###Comments
> DISCLAIMER: This tag type is likely to change in the near future.

[Team Inbox comments](Message-Types) get a tag of the form `influx:3456` to store the id of the commented Inbox item. This tag should not be manually removed from the message.

####Example

```
:influx:123445
```


###Team inbox source

All messages sent to Team Inbox via [Team Inbox Push API](Team-Inbox) get tagged with user-provided `source` parameter. This tag should not be manually removed from the message.

####Example

```
:source:my-fancy-source
```


