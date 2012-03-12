# Files

The Flowdock API provides functionality for searching, downloading and uploading files. Files are accessible via messages, which can contain file paths. There are several [message types](message-types) which can contain messages, but the `file` event is used for [uploading files](messages#/send/files). For example mail messages can contain files as attachments.

## Download file

The REST API path of a file is stored in `path` field of each attachment. See [Message Types](message-types) documentation for examples of attachments.

```
GET /flows/org/flow/files/3929321049bd012fc20f0026b0d8e16c/screenshot.png
```

### Response
```
HTTP/1.1 200 OK
Flowdock-User: 2
Content-Length: 10931
Content-Type: image/png

BINARY DATA
```

## List files

Messages containing files can be fetched by searching with the tag `:file`. If you just want the files uploaded to chat you can filter by the message event `file`. See [documentation for listing messages](messages#/list).

## Upload file

You can [upload files using the Messages resource](messages#/send/files) of the REST API. Here's a quick `curl` example:

```
curl -v -X POST -F "event=file" -F "content=@path/to/file.png" http://BASIC-AUTH@api.flowdock.com/flows/ORGANIZATION/FLOW/messages
```
