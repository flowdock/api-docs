# Developing integrations

An integration with CA Flowdock usually means one thing: getting an activity feed from another tool or service into a [flow](/help/flows), the central place where a team communicates. Integrating with a flow's [team inbox](/help/team_inbox) brings visibility into whatever is happening in the third party service, and lets the team make this activity [the context for their conversations](/help/chat). This type of integration is achieved with an integration application.

CA Flowdock provides a full-fledged framework for creating custom applications that integrate with flows. An integration application can range from being really simple (sends messages to a flow's team inbox) to fully-featured, with bidirectional actions and grouped messages.

We've split this integration guide into two parts. The first part is a hands-on guide for quickly getting messages posted to a flow, while the second part goes into the inherent complexities of the OAuth authentication process and building a production-grade integration.

If anything is unclear, visit our [community](https://community.broadcom.com/enterprisesoftware/communities/communityhomeblogs?CommunityKey=4368e236-21f9-45e2-b86f-da55ab677e23) page for support.

### 1. Quick start guide: Post messages to a flow

This guide should help you test out CA Flowdock's integration API, post messages to flows and see how different fields are presented in the user interface. If you're interested in building an internal-only integration for your team or organization, this guide should suffice.

[Read more](integration-getting-started)

### 2. Creating an integration for all CA Flowdock users

Want to provide an integration for all of CA Flowdock's users? This exhaustive guide is for you. It provides step-by-step instructions on configuring a publishable application, handling OAuth authorization with users and finally getting your integration published.

[Read more](production-integrations)
