# Flowdock integration guide

## Getting started

***Screenshot from a Zendesk ticket showing fields, body, actions…***

<small>*An example of a fully fledged Flowdock integration. You can discuss Zendesk tickets internally in Flowdock and even assign them to you or close them directly from the Flowdock UI.*</small>

To get started on the fast track, simply run the following script to set up everything you need to start firing requests to the API. NOTE: You need to have ruby and the oauth2 gem installed for this to work.

```
> ruby -e "$(curl -fsSL https://gist.githubusercontent.com/livedo/aa5ed39b601ec3dbc741/raw/5f1e4504530ce75762139d281cbda62457924b64/flowdock-oauth.rb)"
```

If you'd rather do things manually, [just follow these simple instructions](???).

Once you have everything set up, let's try out a simple test request. Just replace the token and flow name with what you generated in the step above.

```
> curl -i -X POST -H "Content-Type: application/json" -d '{
"flow_token": "be299b91fffe8e4124986b9b5b44dfc6",
  "event": "activity",
  "author": {
    "name": "Marty",
    "avatar": "https://avatars.githubusercontent.com/u/3017123?v=3"
  },
  "title": "updated ticket",
  "external_thread_id": "1234567",
  "thread": {
    "title": "Polish the flux capacitor",
    "fields": [{ "label": "Dustiness", "value": "5 - severe" }],
    "body": "The flux capacitor has been in storage for more than 30 years and it needs to be spick and span for the re-launch.",
    "external_url": "https://example.com/projects/bttf/tickets/1234567",
    "status": {
      "color": "green",
      "value": "open"
    }
  }
}' https://api.flowdock.com/messages
```

This posts a [message](Messages) into the Flowdock API. Since it includes [thread](Threads) data, a new thread is created and the message is posted in the context of that thread.

***SMALL SCREENSHOT OF THE MESSAGE POSTED ABOVE***

In this example, the message is a notification of some [activity](a new ticket being created) and the thread data sent with the message contains the new state (name, description etc) of that new ticket.

Messages inside a thread can also be [discussion](???), like commenting on a ticket. Threads can contain other kinds of messages, too, like Flowdock chat messages or file uploads.

To post another message to the same thread, you simply need post another message with the same `external_thread_id`. It's a good principle to map those external IDs to the IDs of the items in your app. Example: GitHub uses issue / PR numbers, Zendesk uses ticket IDs. They only need to be unique for that source (project, repository, etc).


## What does a great integration look like?

***ZENDESK SCREENSHOT with legend***

Most Flowdock integrations are with other work-related web apps and send updates from those services into Flowdock. A great integration with a project management tool will send updates when items are created, updated or commented. For example, our Zendesk integration sends updates to Flowdock whenever someone replies to a support ticket or updates its status. Each new update can include both a message ("ticket was updated") and a new updated state for the thread.

When presenting the different items of your app in Flowdock as a thread, there are some great features you can use:

1. Title - Simple enough, this should be the name of the item in your app. For Zendesk, this is the subject line of the support ticket.
1. Fields - all the simple properties. The Zendesk integration uses these for showing the To, From etc fields of the support ticket. Project management tools send all the properties of a specific issue/ticket/story.
1. Body - generally contains the most important content of the item, like an issue description. In some cases this is not needed, like with Zendesk, where new replies and updates make up the most interesting content.
1. Status - A great way to highlight progress. Gives a quick and visible answer to questions like "Has this pull request been merged?" or "Is this support ticket solved?" – you can even color-code the different states (eg. blocked = red, solved = green)
1. [Actions](actions) - An amazing tool for making the integration entirely bidirectional. You can define actions to enable users to complete tasks directly from Flowdock. Popular examples include "Assign to me", "Mark as solved", "Try to build this again" and "Merge pull request".

For detailed descriptions of these fields check out the [threads documentation](threads).

When posting messages into threads, there are a couple of patterns you should follow. When an item in your app gets created, updated or deleted, send an [activity message](message-types#/activity).

***SCREENSHOT OF ACTIVITY MESSAGE***

An activity message only has a couple of unique features:

* The title should fit the format of "Marty McFly **did something**". In other words, good titles are "updated ticket" or "merged pull request".
* If you like, you can include an icon. TODO: ???

If what your sending is a commment to the item in your app, post a [discussion](message-types#/discussion).

TODO: Example of discussion


## Authentication

Flowdock uses OAuth 2.0 for authentication. There are dozens of libraries avaiable for just about all thinkable platforms that take out the biggest hurdles for implementing OAuth.

For a very simple example, check out this [script that handles authentication for testing purposes](https://gist.github.com/livedo/aa5ed39b601ec3dbc741). TODO: Links to examples and libraries!


## Setup / Adding sources

TODO: This should probably be a separate doc or rather let's just make the app management screen amazing.

## Publishing your integration

Once your integration is ready for prime time, send e-mail to support@flowdock.com and we'll publish it add it to our [integrations list](/integrations).