# Files

The Flowdock API provides functionality for searching, downloading and uploading files. The format of file messages is explained in [Message Types](Message Types) documentation.

## List files

Messages containing files can be fetched by filtering messages by type `file` or using special tag `:file` in tag filtering. See [Messages](Messages) documentation for details.

## Upload file

The format of the `content` is different depending on the Content-Type of the request. When using `application/json` the binary is sent within the JSON Base64 encoded. Eg.

```
{
  "data": "iVBORw0KGgoAAAANSUhEUgAAABQAAAAaCAYAAAC3g3x9AAAAGXRFWHRTb2Z0d2FyZQBBZG9iZSBJ\nbWFnZVJlYWR5ccllPAAAAElJREFUeNpiYECA/UD8n0y8nwENUGIYTkOpAhihplMNMFHbhVQ3kIVA\ncOAD/0k1kAFP+DIO3UgZTYej6XA0HY6mw9F0CAEAAQYAk/gtCSEUikYAAAAASUVORK5CYII=\n",
  "content_type": "image/png",
  "file_name": "cabinet_icon.png"
}
```

To send the file using form data, set Content-Type as `multipart/form-data` and set the value of content as the binary part. Here's a `curl` example:

```
curl -v -X POST -F "event=file" -F "content=@path/to/file.png" https://BASIC-AUTH@api.flowdock.com/flows/ORGANIZATION/FLOW/messages
```

## Download file

The REST API path of a file is stored in `path` field of each attachment. See [Message Types](Message Types) documentation for examples of attachments.

```
GET /flows/org/flow/files/3929321049bd012fc20f0026b0d8e16c/screenshot.png
```

### Response
```
HTTP/1.1 200 OK
Flowdock-User: 2
Content-Length: 10931
Content-Type: image/png

DATA
```