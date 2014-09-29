# How to integrate with Flowdock

## Introduction

In this guide you will be creating an application that posts to the inbox of a flow provided by a Flowdock user. The guide is divided into four steps that describe different parts of the development process. If you have any questions, drop us a line at support@flowdock.com

### Steps

1. [Create application in Flowdock](#/create-app)
2. [Authorize application using OAuth 2.0](#/oauth2-authorize)
3. [Create integration for a flow](#/create-integration)
4. [Post to inbox](#/post-inbox)

### Additional steps

* [Publish application](#/publish-app)
* [Integration configuration](#/integration-config)

<div id="/create-app"></div>
## Creating application in Flowdock

The first step is to [create a new application](https://www.flowdock.com/oauth/applications/new) using your own Flowdock credentials. All your developer applications are listed in [Application page](https://www.flowdock.com/account/authorized_applications). The page also contains all the applications you have authorized to use your account.

The application requires following information:

| Name          | Description  |
| ------------- | ------------ |
| Name | Visible name for your application. This is used in the OAuth dialog when the end user is prompted to allow access for your application and when listing your application in inbox sources or search. |
| Redirect URI | The OAuth 2.0 redirect URI of your application. The end user is redirected to this URI after allowing or denying access for your application. This should be an endpoint in your application. |
| Icons | The provided icons are used when listing your application and rendering messages from your application. |

After creating the application, you should see the application id and secret tokens. You will use these when sending OAuth protocol requests to Flowdock from your application.

<div id="/oauth2-authorize"></div>
## Authorizing application using OAuth 2.0

OAuth 2.0 is an authorization framework that enables third-party applications to obtain limited access to Flowdock on the user's behalf without getting their password.

The OAuth 2.0 flow is described in the [Authentication](Authentication#/oauth2) section of the API documentation. Once you have received an authentication token for the end user, you can proceed to the next section of this guide.

<div id="/create-integration"></div>
## Creating an integration for a flow

Before you can post messages using the Activity API, you need to create an integration mapping for your application and a flow. The integration mapping gives you a token that you will use to post to the flow. In addition, the mapping enables searching for items posted by the application and displays the application in the flow's source list.

### Choosing the target flow

There's two options for choosing the target flow: let the user choose the flow or assume that the target flow is provided for you. The former approach works well for cases when you don't intend to publish the application for all Flowdock users. The latter simplifies your application logic and works automatically for all published applications. Both approaches are explained in detail in the following sections.

#### Listing available flows for end user to choose

Now that you have the OAuth access token, you can fetch the user's flows using the [Flows resource](Flows) of [REST API](REST).

Example request:

```
GET https://api.flowdock.com/flows
```

#### Expecting target flow as parameter

With published applications, we list your application in the Inbox Sources list along with a setup link that points to the URL you have defined for the application. You will receive the target flow's identifier as a query parameter named `flow`.

You will still need to fetch [flow's data](Flows) to get the URL for creating an integration for the flow. The flow data, like the name of the flow, will also help in displaying an informative integration screen for the end user.

Example request:

```
GET https://api.flowdock.com/flows/find?id=:flow
```

### Creating integration

Once you have the access token and integrations endpoint URL, you can create the actual integration between the flow and your application.

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

```

| Name          | Description  |
| ------------- | ------------ |
| id | A reference to this particular integration between your application and the flow. This will be provided to you when a Flowdock user attempts to configure your integration from the flow. |
| flow_token | Token for posting to the flow using [Activity API](Activities). |

Storing the reference is optional, but very useful if your application contains settings that can be changed by the end user later on. Read more about [integration configuration](#integration-config).

<div id="/post-inbox"></div>
## Posting to inbox

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

The most important and universal fields are listed below, for full reference on fields and message types, see [Activity API documentation](Activities#/activity).

| Name          | Description  |
| ------------- | ------------ |
| thread_id | This is the identifier used for connecting activities into threads. This should be an unique identifier within the scope of the integration created for this particular flow. For example, if you post two activities with the same identifier and the same flow token, those activities will be end up in the same thread. |

## Additional steps

<div id="/publish-app"></div>
### Publishing your application

<div id="/integration-config"></div>
### Integration configuration

