# Threads

Threads represent entities in external services. They allow tracking events that happen in external services and collecting them into a single view of what has happened to the entity over time. Moreover, all the discussion related to the entity and its events is visible in the thread.

Common examples of thread entities are support tickets, issues in project management software and pull requests in version control software.

A thread in Flowdock API has the following format:

```
{
  "title": "Fix bug in thread API",
  "body": "",
  "external_url": "https://github.com/flowdock/component/pull/42",
  "status": {
    "color": "grey",
    "value": "closed"
  },
  "actions": [
    {
      "@type": "ViewAction",
      "url": "https://github.com/flowdock/component/pull/42/files",
      "name": "Diff",
      "description": "View diff in GitHub",
      "image": "https://s3.amazonaws.com/flowdock-resources/images/diff.png"
    }
  ],
  "fields": [
    {
      "label": "assignee",
      "value": "&lt;a href="mailto:antti@flowdock.com" rel="nofollow"&gt;Antti Pitkänen&lt;/a&gt;"
    }
  ],
  "source": {
    "id": 104,
    "icon": "https://fd-files-eu-qa.s3.amazonaws.com/applications/104/ef8eff491304914a.png",
    "application": "GitHub"
  },
  "activities": 3,
  "internal_comments": 3,
  "external_comments": 5
}
```

| Name          | Description  |
| ------------- | ------------ |
| title | Title of the thread, can contain following HTML tags: `a` |
| body | Body of the thread, HTML allowed |
| external_url | URL to the external resource that is represented by this thread, e.g. Github pull request or Rally user story |
| status.value | Text value of the thread's status |
| status.color | Background color for the above text value. Allowed values: red, green, yellow, cyan, orange, grey, black, lime, purple, blue |
| actions | An array of custom actions for the thread in [schema.org Action format](http://schema.org/Action). The only currently supported action is the [ViewAction](http://schema.org/ViewAction). See above thread data for an example of ViewAction |
| fields | An array of key/value type of propertites for the thread. Value can contain following HTML tags: `a` |
| source | Application that posted this thread. Includes application name and icon |
| activities | Number of [activity messages](thread-messages#/activity) in this thread |
| internal_comments | Number of [chat messages](thread-messages#/message) in this thread |
| external_comments | Number of [discussion messages](thread-messages#/discussion) in this thread |


