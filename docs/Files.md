# Files

The Flowdock API provides functionality for downloading and uploading files. Files are accessible via messages, which can contain file paths. There are several [message types](message-types) which can contain files, but the `file` event is used for [uploading files](messages#/send/files). For example, mail messages can contain files as attachments.

## Download a file

The REST API path of a file is stored in the `path` field of each attachment. See [Message Types](message-types) documentation for examples of attachments.

```
GET /files/12345/3929321049bd012fc20f0026b0d8e16c/screenshot.png
```

### Response
```
HTTP/1.1 302 Found
Flowdock-User: 2
Content-Length: 0
Location: https://fd-files-production.s3-us-east-1.amazonaws.com/12345/3929321049bd012fc20f0026b0d8e16c/...
```

The full file can be downloaded by following the redirect. The URL is only valid for a short time period.

## List files

A list of messages that contain files can be fetched by searching with the tag `:file`. If you just want the files uploaded to the flow's chat, you can filter by the message event `file`. See the [documentation for listing messages](messages#/list).

## Upload a file

You can [upload individual files using the Messages resource](messages#/send/files) of the REST API. Here's a quick `curl` example:

    curl -v -X POST -F "event=file" -F "content=@path/to/file.png" http://BASIC-AUTH@api.flowdock.com/flows/ORGANIZATION/FLOW/messages

Alternatively, as an integration developer, files can be uploaded as attachments to `activity` and `discussion` messages.

    curl -H "Accept: application/json" -F "attachments[id]=@localfile.jpg" -F "author[name]=Uploaded" -F "event=activity" -F 'title="Upload a file"' -F 'body=test <img src="cid:id">' -F "thread[title]='Thread title'", -F "external_thread_id=asdfasdf" -F "flow_token=FLOW_TOKEN" http://api.flowdock.com/messages

Note that here the uploaded file is given an id in attributes and referenced by that name in body with `cid:id`. This is optional, but either all attachments must have an id or it should be omitted for all.
