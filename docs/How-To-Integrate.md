# How to integrate with Flowdock

## Introduction

In this guide you will be creating an application that posts to the inbox of a flow provided by a Flowdock user. The guide is divided into four steps that describe different parts of the development process. If you have any questions, drop us a line at support@flowdock.com

## Creating application in Flowdock

The first step is to [create a new application](https://www.flowdock.com/oauth/applications/new) using your own Flowdock credentials. All your developer applications are listed in [Application page](https://www.flowdock.com/account/authorized_applications). The page also contains all the applications you have authorized to use your account.

The application requires following information:

| Name          | Description  |
| ------------- | ------------ |
| Name | Visible name for your application. This is used in the OAuth dialog when the end user is prompted to allow access for your application and when listing your application in inbox sources or search. |
| Redirect URI | The OAuth 2.0 redirect URI of your application. The end user is redirected to this URI after allowing or denying access for your application. This should be an endpoint in your application. |
| Icons | The provided icons are used when listing your application and rendering messages from your application. |

After creating the application, you should see the application id and secret tokens. You will use these when sending OAuth protocol requests to Flowdock from your application.

## OAuth 2.0

OAuth 2.0 is an authorization framework that enables third-party applications to obtain limited access to Flowdock on the user's behalf without getting their password. 

The OAuth 2.0 flow is described in the [Authentication](Authentication#/oauth2) section of the API documentation. Once you have received an authentication token for the end user, you can proceed to the next section of this guide.
  
## Creating an integration for a flow

### Getting flows of the authenticated user
  * Using flowdock.rb?
  * Do you get the integrations endpoint URL in flow data?

### Creating integration
  * Post to integrations endpoint of the flow

### Handling the response data
  * `id` is a reference to this particular integration between your application and the flow. This will be provided to you when a Flowdock user attempts to configure your integration from the flow.
  
  * Storing the response data is optional, but very useful if your application contains settings that can be changed by the end user later on. Read more about [integration configuration](#integration_configuration)

## Posting to Flowdock

## What's next?

### Integration configuration

### Publishing your application

