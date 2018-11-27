# Doing REST APIs Right
REST APIs are supposed to be simple! However i have often witnessed quite heated discussions, when APIs for specific use cases are built.

Why is that happening?

Technically REST is not a specification. REST provides a set of constraints and principles for building web services APIs. There is a variety of options to achieve similar functionality, some bettter than the others.

Lets challenge ourselves and design asynchronous REST API where almost all resource operations are asyncronous.

We are building a web service that processes and manipulates images. It uses other microservices internally for executing the operations so instant result can not be expected.

# Asyncronous resource creation
The first operation would post the image and expect the image to be uploaded. However the image format should be parsed and validated before storing to the service storage.
We post the resource creation parameters and get back a progress tracking resource.
```http
POST /images/image10 HTTP/1.1
{
  "content":"VGhpcyBpcyBzb21lIGltYWdlLg==",
  "contentType":"image/jpeg"
}
```
- When creating resource use client provided resource identifier whenever possible. This allows consumer to find both resource and its status if it happens to not receive response properly due to some connection issues.
- Server provided identifier can be used if there is reliable way of finding the resource by other key known to the client. 

We get response pointing to processing object in the queue.
```http
HTTP/1.1 202 Accepted
Location: /images/image10/queue/1
```
Here we represent resource specific processing queue job reference. As discussed we might trigger other operations to the resource in the future.
- Avoid sharing the tracking *queue* between different resource entities. There are numerous examples in the web, where you might get the impression, that *queue* resource is shared. It would be very hard to secure the resource access with permissions if the tracking *queue* contains progress information from other entities.

Get status from the tracking resource:
```http
GET /images/image10/queue/1 HTTP/1.1
HTTP/1.1 200 OK
Retry-After: 30
{
  "status" : "RUNNING",
  "result": "N/A",
}
```
- You can *[Retry-After](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Retry-After)* header to suggest polling interval in seconds to the client

Job is done and the resource is available:
```http
GET /images/image10/queue/1 HTTP/1.1
HTTP/1.1 303 See Other
Location: /images/image10
```
# Reporting Failures
If the operation have failed, we need to report the error to the client properly.

