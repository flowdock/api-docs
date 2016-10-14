# Creating a production-grade integration

*This guide dives into the details of building an integration for all CA Flowdock users. If you're interested in just posting messages to a flow or building an internal-only integration, read our [getting started integration guide](integration-getting-started).*

### Introduction

This guide will step you through the process of integrating your web app with CA Flowdock. Once integrated and published, CA Flowdock users can add your integration straight from CA Flowdock to let your app send notifications to their flows. [Integrations](/integrations) that are meant for all CA Flowdock users should be built in a similar manner.

We have created [an example application](https://github.com/flowdock/flowdock-example-integration) using Ruby on Rails. This guide will point you to relevant parts of the code.

If you have any questions, don't hesitate to visit our [community](https://communities.ca.com/community/ca-agile-central/ca-flowdock) page for support.

This guide is divided into the five steps of the development process:

### Steps

1. [Create a developer application in CA Flowdock](#/create-app)
2. [Create an endpoint in your app](#/create-endpoint)
3. [Authorize your app with OAuth](#/oauth2-authorize)
4. [Add your app as a flow source](#/create-integration)
5. [Post messages to the flow](#/post-inbox)

### Additional steps

* [Publish your application](#/publish-app)
* [Integration configuration](#/integration-configuration)
* [Notify users of problems](#/source-problems)
* [Removing an integration](#/integration-remove)

![A support ticket from Zendesk](/images/lifespan-of-a-support-ticket.png)
<small>*An example of a full-fledged CA Flowdock integration from Zendesk. You can discuss Zendesk customer support tickets and even perform basic functions (such as assigning or closing tickets) without leaving CA Flowdock.*</small>

<div id="/create-app"></div>
## 1. Create a developer application in CA Flowdock

The first step is to [create a new CA Flowdock developer application](https://www.flowdock.com/oauth/applications/new). All your developer applications are listed on your [developer applications page](https://www.flowdock.com/oauth/applications).

Follow the instructions in the form. After creating the application, you should see your Application ID and Secret tokens. There are used when sending OAuth requests to CA Flowdock from your application.

<div id="/create-endpoint"></div>
## 2. Create an endpoint in your app
First, you will need a setup endpoint in your web application. The user will be redirected to this endpoint when they start setting up an integration with their flow, and once your application is published, users will find a link to it the flow's "Inbox Settings". For testing purposes, on your developer application's page, there is a flow dropdown that will generate the full setup URI for that specific flow.

Initially, the purpose of the endpoint is to identify the user in your app (e.g. make sure they are logged in or have provided an API token), after which the user is redirected back to CA Flowdock in order to give your app access to the desired flow. If the user authorizes this, they are redirected back to your app with an OAuth access token and information about the target flow. This is explained in more detail below.

<div id="/oauth2-authorize"></div>
## 3. Authorize your app with OAuth

Authorization for your app is handled using OAuth 2.0. Users will give your app permission to access your flow.

OAuth 2.0 is an authorization framework that allows third-party applications to obtain limited access to a service on a user's behalf without getting access their password. The OAuth 2.0 flow is described in the [Authentication](authentication#/oauth2) section of the API documentation. Because of multiple gotchas, we recommend using an established library to implement your OAuth flow.

The endpoint created in step 2 should be specified as the Callback URI in your CA Flowdock developer app. That endpoint needs to obtain an access token from our endpoint (as described in [step 2 of the web application flow description](authentication#/redirect-back)).

In our example Rails application, we have used [Omniauth](https://github.com/intridea/omniauth) combined with [the Omniauth CA Flowdock strategy](https://github.com/flowdock/omniauth-flowdock/). Omniauth takes care of exchanging the authorization code for an access token, as you can see from the [callback endpoint in our example application](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb#L29).

Once you have obtained an access token, you need to create a flow-specific [source](sources) for your web app in the desired flow. This is described in the next section of this guide.

<div id="/create-integration"></div>
## 4. Add your app as a source

Before you can post messages to a flow, you need to use the access token (obtained in the previous step) and the flow parameters (that your setup endpoint received as query parameters) to add your app to the flow's team inbox as a [source](sources). Once done, you will obtain a `flow_token` that can be used to post [messages](messages) to the flow's [team inbox](/help/team_inbox).

When a source is added to a flow, it will show up in the flow's souces list and the application will appear in the flow's inbox filter. The `name` parameter of the source identifies the external entity (eg. a repo for GitHub or a project for a project management tool) that your OAuth application has tied to the particular flow.

To add a source to a flow, you will first need to fetch information about the [flow](flows). It is good practice to display a confirmation screen for the user when creating the source for the specified flow.

Example request:

```
GET https://api.flowdock.com/flows/find?id=:flow
```

This part of the process is implemented as part of the callback [in the example application](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb#L35).

<div id="/post-integration"></div>
### Example: Posting to the sources endpoint

With the access token and flow data, you can create the source – the actual integration – between the flow and your application.

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
| name | This is the data source being connected to the flow. For example, if your application connects a project in an issue tracker, this should be the title of project. If your application is connected to one flow multiple times with different data sources, this helps the end user differentiate between data sources when adding, removing or listing integrations. |

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
| id | A reference to this particular integration between your application and the flow. If integration configuration is enabled for your application, this will be provided when a CA Flowdock user attempts to configure this integration from the flow. |
| name | The data source name, as provided when creating the integration. |
| flow_token | The token for posting [messages](messages) to the flow's team inbox. |
| application | Information about the integration application. |

**Note:** It is important to store the flow_token at this point since retrieving it later on is not possible. It is the (secret) token that is used to authenticate when posting messages.

Storing the reference (id) is optional, but is required if your application contains settings that can be changed by the end user later on. Read more about [integration configuration](#/integration-configuration).

This part of the process is implemented [in routes.rb](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb) in our example application.

You can now test the integration flow by going to your [applications page](https://www.flowdock.com/account/authorized_applications), selecting your application and clicking Start setup process after choosing a target flow. If everything works, you should see a message about a new integration in your flow's team inbox.

<div id="/post-inbox"></div>
## 5. Post messages to the flow

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

The most important fields are listed below. For a full reference on fields and different message types, see the [messages documentation](messages#/thread-messages).

| Name          | Description  |
| ------------- | ------------ |
| flow_token | The token you received after creating the integration between your application and the flow. |
| external\_thread\_id | Identification for the thread that this message is a part of. For example, if you're creating an integration for an issue tracking tool, and you're sending issue change notifications to CA Flowdock, you would use the unique identifier of the issue. For GitHub issues, it would be the issue number, for a customer support tool, it would be the ticket ID. The ID only needs to be unique for this particular source (not the whole application). **This field is required if thread_id is not set.** |
| thread_id | CA Flowdock's thread ID. Can be used to specify the thread into which you're posting this message. **This field is required if external\_thread\_id is not set.** |

The example application uses [a utility class](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/activity.rb) to produce JSON payloads like the example above.

**Note:** Automated messages from integrations should be sent to the flow's team inbox.
Posting to the flow's chat is generally desirable only when e.g. replying to a chat message.

## Additional steps

<div id="/publish-app"></div>
### Publish your application

Published applications will be listed in a flow's Inbox Sources for all CA Flowdock users, and will be featured on our [Integrations page](/integrations). You can submit your application for review by visiting our [community](https://communities.ca.com/community/ca-agile-central/ca-flowdock) page.

<div id="/integration-configuration"></div>
### Integration configuration

Integration configuration is optional for applications. It allows users to change settings related to what's being sent to a flow. For example, if your application provides filtering settings for the messages it posts, you might want to allow the user to change these filtering settings after the integration has already been set up.

If you supply a configuration URI for your application, we will display a configure button for each of a flow's integrations (sources) from your application. This will allow users to go back to your application to change the selected sources' settings.

We will provide a reference to the integration as the `integration_id` query parameter (the `id` parameter that your web application received when the [integration was created](#/post-integration)) along with the `flow` parameter. These two parameters should allow you to identify which integration the end user is trying to configure.

<div id="/source-problems"></div>
### Notifying users of problems

After an integration has been set up with a flow, when a problem with the integration comes up, we may need to inform the flow's users. For example, if the account that was used to set up the integration in the third party service is disabled, we may need to suspend posting activities to the flow.

One way to handle this is to post a normal inbox message to the flow, stating that the integration needs to be reconfigured align with a link to the configuration page. However, inbox messages can be easily missed. A better approach is to set an `error_message` for the configured source using the [Sources API](sources#/update-source). When set, the error message will be displayed prominently in the flow's Inbox Settings and in the flow avatar list. When the problem is resolved, the error message should be cleared by setting `error_message` as an empty string.

<div id="/integration-remove"></div>
### Removing integrations

When an integration (source) is removed in CA Flowdock, you will start receiving a `410 GONE` response when posting [messages](messages) using the `flow_token` of the integration. This means that you should stop posting messages and remove any data you may have stored for this integration in the setup phase.
