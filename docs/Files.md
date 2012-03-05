# Files

## List files

Messages containing files can be fetched by filtering messages by type `file` or using special tag `:file` in tag filtering. See [Messages](Messages) documentation for details.

## Retrieve single file

The REST API path of a file is stored in `path` field of each attachment. The API path is of the following format: `/flows/:org/:flow/files/:uuid/:filename`. See [Message-Types](Message-Types) documentation for examples.

```
GET /flows/:org/:flow/files/:uuid/:filename
```

### Response
```
HTTP/1.1 200 OK
Flowdock-User: 2
Content-Length: 10931
Content-Type: application/octet-stream

_DATA_
```