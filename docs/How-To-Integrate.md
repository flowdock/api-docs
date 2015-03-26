# How to integrate with Flowdock

### Introduction

This guide will step you through the process of integrating your web app with Flowdock. Once integrated, your app will be able to send notifications to Flowdock flows. All future public [integrations](/integrations) should be built in a similar manner.

The guide is divided into the four steps of the development process.

We have created [an example application](https://github.com/flowdock/flowdock-example-integration) using Ruby on Rails. This guide will point you to relevant parts of the code.

If you have any questions, don't hesitate to contact the development team at [support@flowdock.com](mailto:support@flowdock.com).

### Steps

1. [Create an application in Flowdock](#/create-app)
2. [Authorize the application](#/oauth2-authorize)
3. [Add your app as a source](#/create-integration)
4. [Post messages to the flow](#/post-inbox)

### Additional steps

* [Publish your application](#/publish-app)
* [Integration configuration](#/integration-config)
* [Removing integration](#/integration-remove)

<div id="/create-app"></div>
## 1. Create an application in Flowdock

The first step is to [create a new Flowdock developer application](https://www.flowdock.com/oauth/applications/new). All your developer applications are listed on the [Developer applications page](https://www.flowdock.com/oauth/applications).

Follow the instructions in the form. After creating the application, you should see your Application Id and Secret tokens. You will use these when sending OAuth requests to Flowdock from your application.

First, you will need an endpoint in your web application that the user will be redirected to when they start setting up an integration with their flow. Once your application is published, users will find this link in the Flowdock app's "Inbox Settings" view. For testing purposes, on your Developer application's page, there is a flow dropdown that will generate the full setup URI for that specific flow. Your setup endpoint should start by allowing the user to authorize your app to post messages to Flowdock, which is explained in the following step.

<div id="/oauth2-authorize"></div>
## 2. Authorize the application

Your web application will need to be authorized using OAuth 2.0.

OAuth 2.0 is an authorization framework that allows third-party applications to obtain limited access to Flowdock on a user's behalf without getting access their password. The OAuth 2.0 flow is described in the [Authentication](authentication#/oauth2) section of the API documentation. Because of multiple gotchas, we recommend using an established library to implement your OAuth flow.

In your web app, create the endpoint that you specify as the Callback URI in your Flowdock developer app. That endpoint needs to obtain an access token from our endpoint (as described in [step 2 of the web application flow description](authentication#/redirect-back)).

In our example Rails application, we have used [Omniauth](https://github.com/intridea/omniauth) combined with [the Flowdock strategy for Omniauth](https://github.com/flowdock/omniauth-flowdock/). Omniauth takes care of exchanging the authorization code for an access token, as you can see from the [callback endpoint in our example app](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb#L29).

Once you have obtained an access token for the end user, you need to set up your web app as a notifications source for the specified flow. This is described in the next section of this guide.

<div id="/create-integration"></div>
## 3. Add your app as a source

Before you can post messages to a flow, you need to use the access token (obtained in the previous step) and the flow parameters (that your setup endpoint received as query parameters) to add your app to the flow's team inbox as a [source](sources). Once done, you will obtain a `flow_token` that can be used to post ([messages](messages)) into the flow's team inbox.

When a source is added to a flow, it will show up as an inbox filter in that flow. The source also identifies the external entity (eg. a repo for GitHub or a project for a project management tool) that your OAuth application has tied to the particular flow.

To add a source to a flow, you will first need to fetch information about the [flow](flows). It is good practice to display a confirmation screen for the user when creating the source for the specified flow.

Example request:

```
GET https://api.flowdock.com/flows/find?id=:flow
```

This part of the process is implemented as part of the callback [in the example application](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb#L35).

<div id="/post-integration"></div>
### Example: Posting to the sources endpoint

With the access token and flow data, you can create the actual integration between the flow and your application.

The flow data contains an attribute called `links`. The attribute lists available API resources for the flow and what HTTP methods are allowed for the authenticated user. You can find the integrations endpoint URL under the `sources` resource.

Example request:

```
POST https://api.flowdock.com/flows/:org/:flow/sources
```
```json
{
  "name": "My project"
}
```

| Name          | Description  |
| ------------- | ------------ |
| name | This is the data source you are connecting to the flow. For example, if your application connects a project in an issue tracker, this should be the title of project. If your application is connected to one flow multiple times with different data sources, this helps the end user differentiate between data sources when adding, removing or listing integrations. |

Example response data:

```json
{
  "id": 36,
  "name": "My project",
  "flow_token": "965427ebf9997f7aa71384b6f03d608f",
  "application": {
    "id": "4",
    "name": "Example application",
    "configuration_uri": "http://www.example.com"
  }
}
```

| Name          | Description  |
| ------------- | ------------ |
| id | A reference to this particular integration between your application and the flow. This will be provided to you when a Flowdock user attempts to configure your integration from the flow. |
| name | The data source name, as provided when creating the integration. |
| flow_token | The token for posting [messages](messages) to the flow's team inbox. |
| application | Information about the integration application. |

Storing the reference (id) is optional, but very useful if your application contains settings that can be changed by the end user later on. Read more about [integration configuration](#/integration-configuration).

This part of the process is implemented [in routes.rb](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb) in our example application.

You can now test the integration flow by going to your [Applications page](https://www.flowdock.com/account/authorized_applications), selecting your application and clicking Start setup process after choosing a target flow. If everything goes well, you should see a message about a new integration in your flow.

<div id="/post-inbox"></div>
## 4. Post messages to the flow

Your integration should now be ready to start delivering messages to the flow. All messages should be sent to the [messages endpoint](messages) (described below) using `flow_token` to authenticate and identify the target flow.

Example request:

```
POST https://api.flowdock.com/messages
```
```json
{
  "flow_token": "3e2252e2e164d70ebbc5c59b9db629c8",
  "event": "activity",
  "author": {
    "name": "anttipitkanen",
    "avatar": "https://avatars.githubusercontent.com/u/946511?v=2"
  },
  "title": "Opened pull request",
  "thread_id": "WT5yWsIpdvUPxP07lfgQDmLoGQQ",
  "external_thread_id": "github:component:pr:42",
  "thread": {
    "title": "Fix bug in thread API",
    "body": "Body with &lt;b&gt;HTML&lt;b&gt; formatting",
    "external_url": "https://github.com/flowdock/component/pull/42",
    "status": {
      "color": "green",
      "value": "open"
    }
  }
}
```

The most important fields are listed below. For full reference on fields and different message types, see the [messages documentation](messages#/thread-messages).

| Name          | Description  |
| ------------- | ------------ |
| flow_token | The token you received after creating the integration between your application and the flow. |
| external\_thread\_id | Identification for the thread that this message is a part of. For example, if you're creating an integration for an issue tracking tool, and you're sending issue change notifications to Flowdock, you would use the unique identifier of the issue. For GitHub issues, it would be the issue number, for a customer support tool, it would be the ticket ID. The ID only needs to be unique for this particular source (not the whole application). **This field is required if thread_id is not set.** |
| thread_id | Flowdock's thread ID. Can be used to specify the thread, to which you're posting this message. **This field is required if external\_thread\_id is not set.** |

The example application uses [a utility class](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/activity.rb) to produce JSON payloads like the example above.

**Note:** Automated messages from integrations should generally be sent to the flow's team inbox.
Posting to the flow's chat is generally desirable only when e.g. replying to a chat message.

## Additional steps

<div id="/publish-app"></div>
### Publish your application

Published applications will be listed in the Inbox Sources dialog within the Flowdock application for all users, and will be featured on our [Integrations page](/integrations). You can submit your application for review by contacting us at [support@flowdock.com](mailto:support@flowdock.com).

<div id="/integration-configuration"></div>
### Integration configuration

Integration configuration is optional for applications. For example, if your application provides filtering settings for the messages it posts, you might want to allow the user to change these filtering settings later on, after the integration has already been set up.

If you supply a configuration URI for your application, we will display a configure button for each of integration the end user has set up for a flow. This will allow the user to go back to your application to change the integration settings.

We will provide a reference to the integration as the `integration_id` query parameter (the `id` parameter that your web application received when the [integration was created](#/post-integration)) along with the `flow` parameter. These two parameters should allow you to identify which integration the end user is trying to configure.

<div id="/integration-remove"></div>
### Removing integration

Only the end user can remove integrations. When this happens for one of your integrations, you will receive a `410 GONE` response when posting [messages](messages) using the `flow_token` of the integration. This means that you should stop posting messages and remove any data you may have stored for this integration in the setup phase.


