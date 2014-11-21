# Flowdock integration guide

## Getting started

***Screenshot from a Zendesk ticket showing fields, body, actions…***

<small>*An example of a full-fledged Flowdock integration. You can discuss Zendesk customer support tickets over the course of their lifetime in Flowdock, and can even perform basic functions (such as assigning or closing tickets) directly from within Flowdock.*</small>

To get a running start, simply run the following command to set up everything so that you can start firing requests at the API. **NOTE:** You need to have [Ruby](https://www.ruby-lang.org/en/installation/) and the [oauth2 gem](https://github.com/intridea/oauth2) installed for this to work.

```
> ruby -e "$(curl -fsSL https://gist.githubusercontent.com/livedo/aa5ed39b601ec3dbc741/raw/5f1e4504530ce75762139d281cbda62457924b64/flowdock-oauth.rb)"
```

If you'd rather do things manually, [follow these simple instructions](???).

Once you have everything set up, let's try sending a simple request. Just replace the token and flow name with ones that you generated in the step above.

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

This posts a [message](Messages) to the Flowdock API. Since it includes [thread](Threads) data, a new thread is created and the message is posted in the context of that thread.

***SMALL SCREENSHOT OF THE MESSAGE POSTED ABOVE***

In this example, the message describes an [activity](???) that took place in the third party service – in this case, a new ticket was created. The included thread data contains the current state (name, description, etc.) of the newly created ticket.

This brings up an important point when designing your integration: each message is related to some item in the third party service. What the item is depends on the service. With customer support systems, the item is most likely a support ticket. With version control, it can be a commit or pull request. Messages that are related to one item are grouped together as a thread in Flowdock, and the contents of the `thread` parameter should contain the latest state of the item. In other words, an item in the third party service is the same as a thread in Flowdock.

Messages can also be of the type [discussion](???). This type is reserved for conversation that takes place in the third party service – like commenting on a ticket.

Threads can also contain other types of events (like Flowdock chat messages or file uploads), but integrations don't need to be aware of these since they're internal events.

To post another message to the same thread, you simply need post another message with the same `external_thread_id`. It's a good principle to map those external IDs to the IDs of the item in your app. For example, GitHub uses issue or pull request numbers and Zendesk uses ticket IDs. They only need to be unique in the context of that source (project, repository, etc).


## What does a great integration look like?

***ZENDESK SCREENSHOT with legend***

The best Flowdock integrations are with other work-related web apps. These third party apps send updates to Flowdock. A great integration with a project management tool will send updates when items are created, updated or commented on. For example, our Zendesk integration sends updates to Flowdock whenever a new customer support ticket is created, someone replies to a ticket or updates its status. Each new update can include both a message ("ticket was updated") and a new updated state for the ticket.

When presenting your app's different items in Flowdock as a thread, you can use these great features:

1. Title - Simple enough, this should be the name of the item in your app. For Zendesk, this is the subject line of the support ticket.
1. Fields - All the simple properties. The Zendesk integration uses these to show the To, From and other fields of the support ticket. Project management tools send all the properties of a specific issue/ticket/story.
1. Body - This generally contains the most important content of the item, like an issue description. In some cases, this is not needed, such as when new replies and updates make up the most interesting content in Zendesk.
1. Status - A great way to highlight progress. It gives a quick and visible answer to questions like "Has this pull request been merged?" or "Has this support ticket been solved?". The status is shown visibly in the [team inbox](/help/team_inbox). You can even color-code the different states (e.g. blocked = red, solved = green).
1. [Actions](actions) - An amazing tool to make the integration bidirectional. You can define actions to allow users to complete tasks directly from Flowdock. Examples include "Assign to me", "Mark as solved", "Try to build this again" and "Merge pull request".

For detailed descriptions of these fields, check out the [threads documentation](threads).

When posting messages into threads, there are a couple of patterns you should follow. 

[Activity message](message-types#/activity) should be used when an item in your app gets created, updated or deleted.

***SCREENSHOT OF ACTIVITY MESSAGE***

An activity message has a couple of unique features:

* The title should fit the format "Marty McFly **did something**". In other words, good titles are "updated ticket" or "merged pull request".
* If you like, you can include an icon. TODO: ???

If the update is related to a discussion or comment in your app, you should send a [discussion](message-types#/discussion) message.

TODO: Example of discussion


## Authentication

Flowdock uses OAuth 2.0 for authentication. There are dozens of libraries available that remove the pain in implementing OAuth for your app.

For a very simple example, check out this [script that handles authentication for testing purposes](https://gist.github.com/livedo/aa5ed39b601ec3dbc741). 

TODO: Links to examples and libraries!


## Setup / Adding sources

TODO: This should probably be a separate doc, or we should just make the app management screen amazing.


## Publishing your integration

Once your integration is ready for prime time, send an e-mail to support@flowdock.com. After reviewing it, we'll publish it and add it to our [integrations list](/integrations). We'll also be sure to mention it in our monthly newsletter.
