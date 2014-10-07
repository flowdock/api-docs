# How to integrate with Flowdock

## Introduction

In this guide we'll show how to create an integration where a web app posts notifications into a Flowdock flow. This is the basis of almost all of our public [integrations](/integrations). The guide is divided into four steps that describe different parts of the development process. If you have any questions, drop us a line at [support@flowdock.com](mailto:support@flowdock.com).

We have created [an example application](https://github.com/flowdock/flowdock-example-integration) using Ruby on Rails and the guide will point you to relevant parts of the code.

### Steps

1. [Create an application in Flowdock](#/create-app)
2. [Authorize using OAuth 2.0](#/oauth2-authorize)
3. [Create integration for a flow](#/create-integration)
4. [Post messages to inbox](#/post-inbox)

### Additional steps

* [Publish your application](#/publish-app)
* [Integration configuration](#/integration-config)

<div id="/create-app"></div>
## Creating an application in Flowdock

The first step is to [create a new application](https://www.flowdock.com/oauth/applications/new). All your developer applications are listed on the [Developer applications page](https://www.flowdock.com/oauth/applications).

Follow the instructions in the form. After creating the application, you should see the application id and secret tokens. You will use these when sending OAuth protocol requests to Flowdock from your application.

First, you will need an endpoint in your web application, that we will point the user to, when they start setting up an integration to their Flowdock flow using your OAuth app that you created in Step 1. Once your application is published, users will find this link in the Flowdock app's inbox sources view. For testing purposes, on your Developer application's page, there is a flow chooser, that will generate the full setup URI for that specific flow. Your setup endpoint should start by allowing the user to authorize your app to post messages to Flowdock.

<div id="/oauth2-authorize"></div>
## Authorizing application using OAuth 2.0

OAuth 2.0 is an authorization framework that enables third-party applications to obtain limited access to Flowdock on the user's behalf without getting their password. The OAuth 2.0 flow is described in the [Authentication](authentication#/oauth2) section of the API documentation. We recommend using an established library for implementing the OAuth flow.

You'll need to create the endpoint that you specified as the Callback URI in your app. That endpoint needs to take care of using the authentication code to obtain an access token from our endpoint as described in [Step 2 of the web application flow description](authentication#/oauth2) in our OAuth 2 documentation. In our example Rails application we have used [Omniauth](https://github.com/intridea/omniauth) combined with [the Flowdock strategy for Omniauth](https://github.com/flowdock/omniauth-flowdock/). Omniauth takes care of exchanging the authorization code for an access token as you can see in the [code of the callback endpoint in our example app](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb#L29).

Once you have obtained an access token for the end user, you can proceed to the next section of this guide.

<div id="/create-integration"></div>
## Adding your app as a source to a Flowdock flow

Before you can post messages to Flows, you need to use the access token and the flow parameters that your setup endpoint received as query parameters to add your app to a Flowdock flow as a team inbox source by using the [Sources API](/sources). This way you will obtain a flow token, which you can then use to post notifications into the flow's team inbox by using the [Activity API](/activity). Once a source is added to a Flow, it will also show up as a filter in that flow's team inbox. The source will also idetify the external entity (eg. a repo for GitHub or a project for a project management tool), which your OAuth application has tied into the particular Flowdock flow.

To add a source to a flow, you will need to first fetch information about the flow using [the Flows API](flows). It is good practice, to show the user a confirmation screen for creating this source so connecting the Flowdock flow to your application.

Example request:

```
GET https://api.flowdock.com/flows/find?id=:flow
```

This part of the process is implemented as a part of the callback [in the example application code](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb#L29).

<div id="/post-integration"></div>
### Example: Posting to the sources endpoint

With the access token and flow data, you can create the actual integration between the flow and your application.

The flow data contains an attribute called `links`. The attribute lists available API resources for the flow and what HTTP methods are allowed for the authenticated user. You can find the integrations endpoint URL under `sources` resource.

Example request:

```
POST https://api.flowdock.com/flows/:org/:flow/integrations

{
  name: "My project"
}
```

| Name          | Description  |
| ------------- | ------------ |
| name | This is the data source you are connecting to the flow. For example, if your application connects a project in an issue tracker as inbox source, this should be the title of project. If your application is connected to one flow multiple times with different data sources, this helps the end user to differentiate between data sources when adding, removing or listing integrations. |

Example response data:

```
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
| flow_token | Token for posting [thread messages](thread-messages) to the flow. |

Storing the reference is optional, but very useful if your application contains settings that can be changed by the end user later on. Read more about [integration configuration](#integration-config).

This part of the process is implemented [here in the example application code](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/routes.rb).

You can now test the integration flow by going to [Applications page](https://www.flowdock.com/account/authorized_applications), selecting your application and clicking Start setup process after choosing a target flow. If everything goes well, you should see a message about new integration in the flow.

<div id="/post-inbox"></div>
## Posting to inbox

Now your application should be ready to start delivering messages to the flow. All the messages should be sent to the endpoint described below using the `flow_token` to authenticate and identify the target flow.

Example request:

```
POST https://api.flowdock.com/activities

{
  "flow_token": "3e2252e2e164d70ebbc5c59b9db629c8",
  "event": "activity",
  "author": {
    "name": "anttipitkanen",
    "avatar": "https://avatars.githubusercontent.com/u/946511?v=2"
  },
  "title": "Opened pull request",
  "thread_id": "github:flowdock:component:pr:42",
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

The most important and universal fields are listed below, for full reference on fields and different message types, see [thread messages documentation](thread-messages).

| Name          | Description  |
| ------------- | ------------ |
| flow_token | The token you received after creating the integration between your application and the flow |
| thread_id | This is the identifier used for connecting activities into threads. This should be an unique identifier within the scope of the integration created for this particular flow. For example, if you post two activities with the same identifier and the same flow token, those activities will be end up in the same thread. |

The example application uses [a utility class](https://github.com/flowdock/flowdock-example-integration/blob/master/lib/flowdock/activity.rb) for producing JSON payloads like the above example.

## Additional steps

<div id="/publish-app"></div>
### Publishing your application

You can submit your application for review by contact us at [support@flowdock.com](mailto:support@flowdock.com). Published applications will be listed in the Inbox Sources dialog within the Flowdock application.

<div id="/integration-config"></div>
### Integration configuration

Integration configuration is optional for applications. For example, if your application provides filtering settings for the messages it posts, you might want to enable the user to change these filtering settings later on after the integration has already been set up.

If you supply a configuration URI for your application, we will display a configure button for each of integration the end user has set up for a flow. This will enable the end user to change the integration settings afterwards by going back to your application.

We will provide a reference to the integration as `integration_id` query parameter that is appended to the configuration URI along with the `flow` parameter. These two parameters should allow you to identify which integration the end user is trying to configure, provided that you stored the `id` attribute of the integration you created in [the previous step](#/post-integration).
