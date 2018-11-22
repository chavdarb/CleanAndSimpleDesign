# Doing REST APIs Right
REST APIs are supposed to be simple! It seems however i have witnessed quite heated discussions, when APIs for specific use cases are built. Technically REST is not a specification. REST provides a set of constraints and principles for building web services APIs. There is a variety of options to achieve similar functionality, some bettter than the others.

In this article, we will be designing asynchronous REST APIs. We will be trying to avoid some general mistakes and hopefully suggest a good approach.

We will be building APIs for fictional image text recognition service. It will be a step by step approach so we provide a bit of reasoning along the way.

# Asyncronous resource creation
The first operation would post the resource and expect the image recognition service to p
We post the resource creation parameters and get back a progress tracking resource.
```http
POST /images/image10 HTTP/1.1
{
  "content":"VGhpcyBpcyBzb21lIGltYWdlLg=="
}
```

We get response pointing to processing object in the queue.
```http
HTTP/1.1 202 Accepted
Location: /images/image10/queue/1
```

