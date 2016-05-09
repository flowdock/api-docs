# Integration quick start guide

*This is a quick start guide for getting messages from an external source into your flow. For a more in-depth guide on how to build a production-quality integration with CA Flowdock, you can read [part 2](production-integrations) of our integration guide.*


* [Getting started](#/getting-started)
* [What does a great integration look like?](#/a-great-integration)
* [The components of an integration message](#/components-of-a-message)
* [Message types](#/message-types)
* [Share your integration](#/share-your-integration)
* [An example integration app: Polldock](#/an-example-integration-app)

<div id="/getting-started"></div>
### Getting started

Getting content into a flow requires the creation of two things:

1. A developer (integration) application
2. A [source](/api/sources) for each flow into which you want to send messages

Start off by [creating a developer application](https://www.flowdock.com/oauth/applications), then generate a [source](/api/sources) (and flow token) for the desired flow(s). For testing purposes, you can generate sources using the Source generation tool that is available on the developer application's page, under Tools. Note that the flow token is only displayed once: when you create the source. Be sure to store it some place safe!

Once you've created a source, let's try sending a simple request. You can use the snippet below by replacing the `flow_token`  parameter with your source's flow token.

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

This posts a [message](messages) to the CA Flowdock API. Since it includes [thread](threads) data, a new thread is created and the message is posted in the context of that thread.

You can think of a thread as one "item" in the service to be integrated. For example, in customer support tools it might be a support ticket. With version control systems it can be an issue or pull request. In monitoring services, it could be an issue or alert. Messages related to this one item are grouped into one thread in CA Flowdock. In other words, **an item in the service to be integrated is the same as a thread in CA Flowdock**.

![The message posted above](/images/example-message-polish-the-flux-capacitor.png)

In the curl command above, the message describes an [activity](message-types#/activity) that took place in the third party service: a new ticket was created. The included thread data contains the current state (name, description, etc.) of the newly created ticket. The contents of the `thread` parameter should always contain the latest state of the item.

To post another message to the same thread, simply post another message with the same `external_thread_id` value. It's a good principle to map these external IDs to the IDs of the items in your app. For example, GitHub uses issue or pull request numbers and Zendesk uses ticket IDs. They only need to be unique in the context of the source (e.g. project or repository) that was created.

We can update the status of the ticket by changing the values in the status field and POSTing the above curl command again.

```
    "status": {
      "color": "grey",
      "value": "closed"
    }
```

The item will update in real-time along with a message saying that the ticket was updated.

![The update that just happened](/images/update-happened.png)

That's it! These are the basics that are needed in order to get content into your flow: a developer application, and a source (along with its flow token) for each flow.

<div id="/a-great-integration"></div>
### What does a great integration look like?

The best CA Flowdock integrations are with other work-related web apps. These third party apps send activity updates to CA Flowdock. A great integration with a project management tool will send updates when items are created, updated or commented on.

For example, our Zendesk integration sends updates to CA Flowdock whenever a new customer support ticket is created, someone replies to a ticket or updates its status. Each new update can include both a message ("ticket was updated") and a new updated state for the ticket. You can see it in action [here](https://www.youtube.com/watch?v=dYuhh83EtxU).

<div id="/components-of-a-message"></div>
### The components of an integration message

![A support ticket from Zendesk with legend](/images/lifespan-of-a-support-ticket-legend.png)

When presenting your app's items in CA Flowdock as a thread, you can use these components:

1. **Title** - Simple enough. This should be the name of the item in your app. For Zendesk, a customer support tool, this is the subject line of the support ticket.
1. **Icon** - Each integration has its own icon. This is configured for the [developer application](https://www.flowdock.com/oauth/applications).
1. **Fields** - All the simple properties – in other words, a list of key-value pairs. The Zendesk integration uses these to show the To, From and other fields of the support ticket. Project management tools send all the properties of a specific issue/ticket/story.
1. **Body** - This generally contains the most important content of the item, such as an issue description. In some cases, this is not needed, such as when new replies and updates make up the most interesting content in Zendesk.
1. **Status** - A great way to highlight progress. It gives a quick and visible answer to questions like "Has this pull request been merged?", "What's the status of our server?" or "Has this support ticket been resolved?". The status is shown visibly in the [team inbox](/help/team_inbox). You can even color-code the different states (e.g. blocked = red, solved = green).
1. **[Actions](thread-actions)** - Now we're getting to the awesome stuff. This allows you to [build bidirectional integrations](how-to-create-bidirectional-integrations). You can define actions that allow users to complete tasks directly from CA Flowdock. Examples include "Assign to me", "Mark as solved", "Try to build this again" and "Merge pull request".

For detailed descriptions of these fields, check out the [threads documentation](threads).

<div id="/message-types"></div>
### Message types

When posting messages into threads, there are a generally two types of messages that should be used: [activities](message-types#/activity) or [discussions](message-types#/discussion). To control which kind of a message is being sent, set the `event` field as "activity" or "discussion".

#### Activities

[Activity messages](message-types#/activity) should be used when an item in your app gets created, updated or deleted.

![An activity message](/images/activity-message.png)

The *title* should follow the format "Marty McFly **did something**". In other words, examples of a good title are "updated ticket" or "merged pull request".

#### Discussions

If the update is related to a discussion or comment in your app, you should send a [discussion](message-types#/discussion) message.

![A discussion message](/images/discussion-message.png)

A discussion message's most important field is *body*. You can also set the *title* similarly as with activity messages ("said" or "commented" are good candidates).

<div id="/share-your-integration"></div>
### Share your integration

Want to share your integration application with all of CA Flowdock's users? Read our [more in-depth guide](production-integrations) for information about authentication, source generation and publishing the application.

<div id="/an-example-integration-app"></div>
### An example integration app: Polldock

![Polldock in its own browser window and in CA Flowdock](/images/polldock-screenshot.png)

We've written an example polling app that updates CA Flowdock with new polls and votes, has a discussion integration, and even allows users to vote directly from CA Flowdock. The source code is [available on GitHub](https://github.com/flowdock/flowdock-example-integration). You can use it as an example for building your own integration.
