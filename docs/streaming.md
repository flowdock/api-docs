# Streaming API

**Use the streaming API to listen to messages, events and changes in flows.** The streaming API allows real-time access to flows. Unlike other APIs, all requests to the streaming API should be made to *stream.flowdock.com*. Authentication is identical to [the REST API Authentication](authentication).

## Connecting

To connect to the Streaming API, form a HTTP request and consume the resulting stream for as long as is practical. The CA Flowdock servers will hold the connection open indefinitely (barring a server-side error, excessive client-side lag, network hiccups or routine server maintenance). If the server returns an unexpected response code, clients should a wait few seconds before trying again.

## Content Types

The streaming API supports two different content types: JSON stream and Event-Stream. Applications can use the `Accept` header or `accept` query string parameter to choose the content type: `application/json` for the JSON stream and `text/event-stream` for Event-Stream. If no content type is specified, the streaming API will use `application/json`.

Content is encoded as UTF-8.

### JSON Stream (`application/json`)

The JSON stream returns messages as JSON objects that are delimited by carriage return (`\r`). Newline characters (`\n`) may occur in messages, but carriage returns should not.

Parsers must be tolerant of occasional extra newline characters placed between messages. These characters are sent as periodic "keep-alive" messages to tell clients and NAT firewalls that the connection is still alive during low message volume periods.

### Event-Stream (`text/event-stream`)

Event-Stream follows the HTML5 [Server-Sent Events specification](http://dev.w3.org/html5/eventsource/). Each event contains a JSON-encoded message.

Each event message contains id string that can sent to server when reconnecting. The server then responds by streaming all messages since the last disconnection.

## Resources

### GET /flows

Returns all messages from flows specified in the `filter` query parameter.

For example, to stream the flows `example/main` and `example/flow`, a request should be made to `https://stream.flowdock.com/flows?filter=example/main,example/flow`.

#### Parameters

| Name          | Description  |
| ------------- | ------------ |
| filter | Comma separated list of flows, e.g. `example/main,example/flow`. You can also use flow ids. Either this or `user` needs to be set. |
| user | When set to `1`, stream private messages that are sent to the user. Either this or `filter` needs to be set. |
| accept | Define the content-type if the Accept header cannot be used. |
| active | Show user as active in CA Flowdock. Defined values `true` and `idle`. If not present, user will appear offline. |


### GET /flows/:organization/:flow

Convenience method to stream messages from a single flow. Uses same parameters as `GET /flows`.

## Browser Example

The [EventSource API](http://www.w3.org/TR/eventsource/#the-eventsource-interface)
is implemented in most browsers. Even older browsers can use this interface via a
[polyfill](https://github.com/Yaffle/EventSource).

```javascript
    var stream = new EventSource('https://stream.flowdock.com/flows?filter=<flow-id>&access_token=<oauth-token>')
    stream.onmessage = function(event) {
      var message = JSON.parse(event.data);
      // handle message
    }
```

## Ruby Example (JSON stream)

```ruby
    require 'eventmachine'
    require 'em-http'
    require 'json'

    # token = your Flowdock access token
    # organization = parametrized organization name
    # flow = name of flow in URL

    http = EM::HttpRequest.new(
      "https://stream.flowdock.com/flows/#{organization}/#{flow}",
      :keepalive => true, :connect_timeout => 0, :inactivity_timeout => 0)
    EventMachine.run do
      s = http.get(:head => { 'Authorization' => [token, ''], 'accept' => 'application/json'})

      buffer = ""
      s.stream do |chunk|
        buffer << chunk
        while line = buffer.slice!(/.+\r\n/)
          puts JSON.parse(line).inspect
        end
      end
    end
```

## Ruby Example (Event-Stream)

Another Ruby example that uses the [em-eventsource](https://github.com/AF83/em-eventsource) library and Event-Stream content type: [https://github.com/AF83/em-eventsource/blob/master/examples/flowdock.rb](https://github.com/AF83/em-eventsource/blob/master/examples/flowdock.rb)
