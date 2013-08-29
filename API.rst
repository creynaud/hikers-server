Hikers Log server API
=====================

This API is inspired by the couchdb document API: http://wiki.apache.org/couchdb/HTTP_Document_API

Getting a document
------------------
To retrieve a document, simply perform a GET operation at the document's URL::

    GET /api/document/some_doc_uuid HTTP/1.0
    Accept: application/json

Here is the server's response::

    HTTP/1.1 200 OK
    {
        "uuid":"some_doc_uuid",
        "rev":"1-946B7D1C",
        "type":"hike",
        "name":"Grande Casse",
        "owner":"thebrain@acme.com",
        "date": "2013-08-29T14:30:55Z"
    }

Creating a document
-------------------
The POST or PUT operations can be used to create a new document::

    POST /api/document/ HTTP/1.0
    Content-Type: application/json
    Accept: application/json
    {
        "uuid":"some_doc_uuid",
        "type":"hike",
        "name":"Grande Casse",
        "owner":"thebrain@acme.com",
        "date": "2013-08-29T14:30:55Z"
    }

Note that the uuid is not mandatory in the case of a creation. If the uuid is not specified, the server will generate one.

Here is the server's response::

    HTTP/1.1 201 Created
    Content-Type: application/json
    {"ok":true, "uuid":"some_doc_uuid", "rev":"1-946B7D1C"}

Updating a document
-------------------
The POST or PUT operations can be used to update a document::

    PUT /api/document/ HTTP/1.0
    Content-Type: application/json
    Accept: application/json
    {
        "uuid":"some_doc_id",
        "rev":"1-9242ABCD",
        "type":"hike",
        "name":"Grande Casse",
        "owner":"thebrain@acme.com",
        "date": "2013-08-29T14:30:55Z"
    }

Here is the server's response::

    HTTP/1.1 200 Updated
    Content-Type: application/json
    {"ok":true, "uuid":"some_doc_uuid", "rev":"2-946B7D1C"}

Note that if you don't specify the "rev" in the request json body or
specify a "rev" that is not the current object revision, then the a conflict
will occur::

    HTTP/1.1 409 Conflict
    Content-Type: application/json
    {"error":"conflict"}

Deleting a document
-------------------
To delete a document, perform a DELETE operation at the document's location, passing the rev parameter with the document's current revision::

    DELETE /api/document/some_doc_uuid?rev=2-1582603387 HTTP/1.0

And the response::

    HTTP/1.1 200 OK

Deleting a document without specifying the revision will result in a conflict response::

    HTTP/1.1 409 Conflict
    Content-Type: application/json
    {"error":"conflict"}

Getting changes on documents
----------------------------
To get the changes on a specific list of documents, perform a POST or a PUT to the changes URL::

    POST /api/changes/ HTTP/1.0
    Content-Type: application/json
    Accept: application/json
    {
        [
            {"uuid":"uuid1", "rev":"1-967a00df"},
            {"uuid":"uuid2", "rev":"2-7051cbe5"},
            {"uuid":"uuid3", "rev":"2-eec205a9"}
        ]
    }

The response will contain the documents status ("updated", "did_not_change" or
"does_not_exist"). In case of an update, the new document will be available in
the response::

    HTTP/1.1 200 OK
    Content-Type: application/json
    {
        [
            {"uuid":"uuid1", "status": "updated", "doc":
                {
                    "uuid":"uuid1",
                    "rev":"2-9242ABCD",
                    "type":"hike",
                    "name":"Grande Casse",
                    "owner":"thebrain@acme.com",
                    "date": "2013-08-29T14:30:55Z"
                }},
            {"uuid":"uuid1", "status": "did_not_change"},
            {"uuid":"uuid3", "status": "does_not_exist"},
        ]
    }

Note that the "does_not_exist" status does not mean that the document never
existed. It could have been deleted.

Bulk creations and updates
--------------------------
To be defined.
