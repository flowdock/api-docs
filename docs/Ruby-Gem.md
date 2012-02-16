# Ruby Gem

Use the Flowdock API Ruby Gem to get quickly started with developing applications for Flowdock. It supports all the available API features and can get you up and running in no time.
Install

## Install Flowdock API client using RubyGems:

```
gem install flowdock
```

Or download the source code from the [Flowdock API project](https://github.com/flowdock/flowdock-api) on GitHub.

## Example

Sending message to flow's Influx is easy, you just need to know the API Token of the flow you are using in order to authenticate.

```
require 'flowdock'
          
# create a new Flow object with API Token and sender information
flow = Flowdock::Flow.new(:api_token => "56188e2003e370c6efa9711988f7bf02", 
  :source => "myapp", 
  :from => {:name => "John Doe", :address => "john.doe@yourdomain.com"})
            
# send message to the flow
flow.send_message(:subject => "Greetings from Flowdock API Gem!", 
  :content => "This is a test message.", 
  :tags => ["cool", "stuff"])
```
