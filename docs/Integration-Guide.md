# Flowdock integration guide

## Getting started

> Screenshot from a Zendesk ticket showing fields, body, actions…

*An example of a fully fledged Flowdock integration. You can discuss Zendesk tickets internally in Flowdock and even assign them to you or close them directly from the Flowdock UI.*

To get started on the fast track, simply run the following script to set up everything you need to start firing requests to the API. NOTE: You need to have ruby and the oauth2 gem installed for this to work.

```
> curl URL_TO_SCRIPT | bash
```

If you'd rather do things manually, [just follow these simple instructions](???).

Once you have everything set up, let's try out a simple test request:

```
curl -i -X POST -H "Content-Type: application/json" -d '{
"flow_token": "be299b91fffe8e4124986b9b5b44dfc6",
  "event": "activity",
  "author": {
    "name": "Tom",
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

Just replace the token and flow name with what you generated in the step above.

This posts a [message](Messages) into the Flowdock API. Since it refers to a [thread](Threads), a new thread is created and the message is posted in the context of that thread. 

> SCREENSHOT

In this example, the message is a notification of some [activity](a new ticket being created) and the thread data sent with the message contains the new state (name, description etc) of that new ticket.

Messages inside a thread can also be [discussion](???), like commenting on a ticket. Threads can contain other kinds of messages, too, like Flowdock chat messages or file uploads.

The threads can show a few types of different information (body, custom fields) and have different [actions] defined for them (eg. close ticket, assign to me).


## What does a great integration look like?

> SCREENSHOT with legend

Most Flowdock integrations are with other work-related web apps and send updates from those services into Flowdock. A great integration with a project management tool will send updates when items are created, updated or commented. For example, our Zendesk integration sends updates to Flowdock whenever someone replies to a support ticket or updates its status. There are a couple of great features you can use:

* Fields - all the simple properties. The Zendesk integration uses these for showing the to, from etc fields of the support ticket. Project management tools send all the properties of a specific issue/ticket/story.
* Body - generally contains the most important content of the item, like an issue description. In some cases this is not needed, like with Zendesk, where new replies and updates make up the most interesting content.
* Status - A great way to highlight progress. Gives a quick and visible answer to questions like "Has this pull request been merged?" or "Is this suppor ticket solved?" – you can even color-code the different states (eg. blocked = red, solved = green)
* Actions - An amazing tool for making the integration entirely bidirectional. You can define actions to enable users to complete tasks directly from Flowdock. Popular examples include "Assign to me", "Mark as solved", "Try to build this again" and "Merge pull request".

For a detailed description of these fields check out the [threads documentation](threads).


## Authentication

Flowdock uses OAuth 2.0 for authentication. There are dozens of libraries avaiable for just about all thinkable platforms that take out the biggest hurdles for implementing OAuth.

For a very simple example, check out this [script that handles authentication for testing purposes](url_to_testing_script). TODO: Links to examples and libraries!


## Setup / Adding sources

TODO

## Publishing your integration

TODO