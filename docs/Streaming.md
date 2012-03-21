# Streaming API

**Use the streaming API to listen to messsages, events and changes in flows.** Streaming API allows real-time access to flows. Unlike other APIs, all requests to Streaming API should be made to *stream.flowdock.com*. Authentication is identical to [the REST API Authentication](Authentication).

## Connecting

To connect to the Streaming API, form a HTTP request and consume the resulting stream for as long as is practical. Our servers will hold the connection open indefinitely, barring server-side error, excessive client-side lag, network hiccups, routine server maintenance. If server returns unexpected response code, clients should wait few seconds before trying again.

## Content Types

Streaming API supports two different content types, JSON stream and Event-Stream. Applications can use `Accept` header or `accept` query string parameter to choose content type, `application/json` for JSON stream and `text/event-stream` for Event-Stream. If no content type is specified, Streaming API will use application/json.

Content is encoded as UTF-8.

### JSON Stream (`application/json`)

JSON stream returns messages as JSON objects delimited by carriage return (`\r`). Newline characters (`\n`) may occur in messages, but carriage returns should not.

Parsers must be tolerant of occasional extra newline characters placed between messages. These characters are placed as periodic "keep-alive" messages, should the stream of messages temporarily pause to allow clients and NAT firewalls to determine that the connection is indeed still valid during low volume periods.

### Event-Stream (`text/event-stream`)

Event-Stream follows HTML5 [Server-Sent Events specification](http://dev.w3.org/html5/eventsource/). Each event contains a JSON-encoded message.

`Last-Event-ID` header is *not supported*.

**API for Event-Stream may change in future.**

## Resources

### GET /flows

Returns all messages from flow specified in query parameter. Clients must specify the flows using _filter_ parameter, which contains comma-separated list of flows.

For example, to stream flows `example/main` and `example/flow`, request should be made to `https://stream.flowdock.com/flows?filter=example/main,example/flow`.

### GET /flows/:organization/:flow

Conveniency method to stream messages from a single flow.

## Example

    require 'eventmachine'
    require 'em-http'
    require 'json'

    # token = your Flowdock access token
    # organization = Flowdock subdomain
    # flow = name of flow in URL

    http = EM::HttpRequest.new("https://stream.flowdock.com/flows/#{organization}/#{flow}")
    EventMachine.run do
      s = http.get(:head => { 'Authorization' => [token, ''], 'accept' => 'application/json'}, :keepalive => true, :connect_timeout => 0, :inactivity_timeout => 0)

      buffer = ""
      s.stream do |chunk|
        buffer << chunk
        while line = buffer.slice!(/.+\r\n/)
          puts JSON.parse(line).inspect
        end
      end
    end

