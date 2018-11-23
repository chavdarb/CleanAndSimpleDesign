# Doing REST APIs Right
REST APIs are supposed to be simple! However i have often witnessed quite heated discussions, when APIs for specific use cases are built.

Why is that happening?

Technically REST is not a specification. REST provides a set of constraints and principles for building web services APIs. There is a variety of options to achieve similar functionality, some bettter than the others.

Lets try to challenge ourselves and imagine we are designing asynchronous REST APIs where almost all resource operations are asyncronous.

We are building a service that processes and manipulates images using other micro-services. Since operations are long running and distributed, we would try our best to achieve both fault tolerance and transparency.

# Asyncronous resource creation
The first operation would post the image resource and expect the image to be processed before storing to the service storage.
We post the resource creation parameters and get back a progress tracking resource.
```http
POST /images/image10 HTTP/1.1
{
  "content":"VGhpcyBpcyBzb21lIGltYWdlLg=="
}
```
- We use client provided resource identifier here. This allows consumer to find both resource and its status if it happens to not receive response properly due to some connection issues. Also we would not desire our service to get bombarded  by misbehaving client.

We get response pointing to processing object in the queue.
```http
HTTP/1.1 202 Accepted
Location: /images/image10/queue/1
```
- Here we represent resource specific processing queue job reference. As discussed we might trigger other operations to the resource in the future

Get status from the tracking resource
```http
GET /images/image10/queue/1 HTTP/1.1
HTTP/1.1 200 OK

{
  "status" : "RUNNING",
  "statusMessage": "Executing image validation.",
}
```
