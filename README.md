# planets-project

## Reading Our Planet DATA

We've learned that it is a good idea to stream large datasets. We are going to use the streaming capability of Node to read in data from Kepler. So let's apply what we learned.

We've seen that the return value of the `require('csv-parse')` is a function that we can call like so:

> `parse()`

and that function returns an event emitter that deals with stream of data coming in from file. But the `parse()` function does not deal with files directly. It only knows about streams. This is were Node built-in **file system** module is going to help us.

### What File System (fs) functionality are we going to use?

Just like the **File System** module allows us to _open a file_, and to read it as a string of text, it also provides a function called `fs.createReadStream(path[, options])`. This allows us to open a file as a readable stream.

**Let's find out what it means**? We can see that the function takes in the name or path to the file that we're opening and that it gives us back a readable stream. This readable stream is a kind of event emitter that emits various named events depending on what is currently happening to that file. For example, if file was **closed**, or a piece of **data** is read from that file. There is also an event when there is no more data available to be consumed from that stream (i.e we reached the end of the file). We also have one event for if there is an **error** (maybe the file does not exit). We can use these events to read our file in piece by piece. And every time a piece of data is received we can do some processing on that data, while we wait for the rest of the data to be read in.

So let's read our file like so:

```js
fs.createReadStream('./kepler-data.csv')
```
This code gives us an event emitter which we can react from using the `on` function.

```js
fs.createReadStream('./kepler-data.csv')
    .on('data', (data) => {
        
    })
```

With each chunk of data that comes in, let create an array which we'll called _results_ and make it a constant so we can't re-assigned the results but we can update the contents by pushing values. So we push data that comes in and stores it in the **results** array

```js
    fs.createReadStream('./kepler-data.csv')
    .on('data', (data) => {
        results.push(data)
    })
```

When we reached the end of our data, let's just print the result that we've received. We can actually call the `on()` function again to form what's called  **chaining of different handlers** on our read stream. Because the `on()` function returns the event emitter that it was called on, we can keep adding this `.on()` handlers for different events we might received. So on the `end` event, we are not going to receive any data, but we can console.log our _results_, and we can also say that we are done processing the file 

```js
    fs.createReadStream('./kepler-data.csv')
    .on('data', (data) => {
        results.push(data)
    })
    .on('end', () => {
        console.log(results)
        console.log('Done')
    })
```

That might be it, but let's be responsible and react to errors that might occur when reading the file.

```js
    fs.createReadStream('./kepler-dataa.csv')
    .on('data', (data) => {
        results.push(data)
    })
    .on('error', (err) => {
        console.error(err)
    })
    .on('end', () => {
        console.log(results)
        console.log('Done')
    })
```

## Introduction to HTTP Response and Requests

We've seen that when you browse to a site, we used this system called DNS to figure out where that site lives, which IP address we should use when communicating with it.

And we can then make **requests** to our HTTP web server to get data back from it. The thing that define how our web server respond to these requests is our **API**. The API tells us what kind of functions the server should support and how those functions should be used. Functions like for example **_getting a list of friends_**, or **_getting their messages_**, or **_getting their photos_**.

We can implement our API on the server in Node.js, in Python, or any other programming language. What matters is that the language that we used when reacting to these requests and responding to them is HTTP, which is the **common way** that the browser and the server can use to understand what both sides are saying. So the browser is speaking HTTP to the server and the server can respond, because the server also speaks HTTP.

### So what does this language look like, what are the words the HTTP uses.

For that we can browse to the Mozilla Developer Network Documentation (MDN) which has this excellent reference for the **HTTP Requests Methods** which are also sometimes called **_HTTP Verbs_**. They represent all of the possible actions that you might want to perform on the server side as browser.

- So a browser might want to **GET** some data or a file stored on that server.
- the browser might want to **POST** some data, which is another of saying _submit_ or _send_ something to the server (a message or maybe a photo).
- or maybe the browser want to **DELETE** some data that the server has about us as user

There are few more methods that HTTP support that are less common to use but we'll go over them when it makes sens to. That's it. We can see on the MDN page, all of the HTTP methods, all the words in the HTTP language that we need to learn to build the app of our dream.

HTTP has become the language of the web because it's so simple. In the next section, let's take a look at the kind of messages that we can send and receive in HTTP using the methods that we've seen here.


## HTTP Requests

What kind of messages do we send back and forth in HTTP? What an HTTP conversation look like? Remember that an API stands for Application Programming Interface. It specifies how two applications talk to each other, for example a browser and a web server. And On the web, it's generally the browser making the requests and the server responding to those requests. If we think of an API, almost like a set of phrases in a language that we can put together to communicate an idea or to get some work done, then it's generally the browser that starts the conversation and asks questions of the server, waiting for it response before potentially making another request. It's almost like the browser interviewing the server.

### So what kind of things can we ask of our web server?

With HTTP, our API is made up of operations using these methods like: GET, POST which we saw that are used on different collections of Data like for example a list of friends (GET /friends) or messages (GET /messages) or photos (GET /photos). We can combine **the name of the method** with the **collection name** to get the data that the server has on who our friends are. Or we can add an Id to the name of our collection to specify that we want to get data about a specific friend (e.g: get data on the friend of id 5, GET /friends/5)

We can generally categorize our requests into requests that are been made on collections of data and requests that are been made on specific item in the same collections. This apply to all of our HTTP verbs. So we can also:

```bash
    POST /messages : if we wanted to save a message and send it to our friends
```

But in general it does not make sens to try to POST to a specific message. Because we  generally only add to collections of data and not to individual items. So the following API phrase is not correct:

```bash
    POST /messages/15
```

But it is possible for us to want to update a specific message to for example change some text or add a photo. And so for that we'll use a **PUT** which allows us to replace the message that we specify with message that we pass in through that request (also called the body of the request)

```bash
    PUT /messages/15
```

We might decide that we are no longer happy with our messages and want to **DELETE** it or maybe we want to remove a specific friend.

```bash
    DELETE /messages/15

    DELETE /friend/5
```

These are possible HTTP requests.

Every one of these request have 4 main parts:

- First is the **METHOD** (GET, POST, PUT, DELETE, PATCH): defines the operations that the browser want to make to the server.
- Each verb is matched up with a **PATH** to a collection or a specific item of a collection. This **PATH** is also sometimes called the **RESOURCE** that we're accessing on the backend.
- We send data to the server by adding a **BODY** to our request which contains the data that we are sending from the browser to the server. This could be in plain text or in a variety of formats. The most common format being JSON. (e.g `{text: "hello", photo: "smile.jpg"}`). Usually we'll have a body on **POST** requests and on **PUT** requests where we are updating data on the server. But we we will not have **BODY** on **GET** or **DELETE** requests, because on those requests the server knows everything it needs to from the METHOD and PATH.
- And last (but not the least), the fourth part of every request which is the **HEADERS**. These are _optional_ properties that we can specify on requests to send additional metadata to the servers. That is _data about the data_ that we're are sending, _data about the request_ (E.g. _**size**_ about the data we're sending', any authentication information we need to send so that the server knows that we're allow to perform that operation with our user). These are _optional_ depending on our use case. But there is one HEADER that every single request needs to have. It is the **HOST** header which specify which server your request is being sent to, including sometimes the **PORT** number for that site. The **HOST** needs to be specify to verify that we're sending our request to the right server.

That's pretty much it. Using these few  parts, we can send over any request we can imagine over to the server so that it can respond to our request.

We'll find all about the response in the next section.

## HTTP Response

We've seen how the browser sends messages or requests to the server by passing:

- the **METHOD**
- the **PATH** to the resource or collection
- sometimes the **BODY** with some data to send to the server
- and some optional list of **HEADERS** with metadata about that request.

### What does the server respond with?

**Responses** in HTTP are even simpler than requests.

With Responses with have three (3) main parts:

  - Like in request, we optional list of **HEADERS**
    - (e.g. "**Content-Type: application/json**") which is a HEADER that we might see both in request and response. It tells us the type of data that is being sent in the body of request or in the body of the response.
  - The second main part of the response is the **BODY** that contains the data that we're fetching from the server. If we set the Content-Type as _application/json_ on the server side, that means that the server is sending a JSON object (e.g. a message from the messages collection like so `{text: "Hi!", photo: "wave.jpg"}`)
- The response also include a **STATUS CODE** which tells us if the request was successful. If not it sends us an **ERROR_CODE** telling us what went wrong. The status code that we want to is **200** which indicates that the response is a successful one. 
  - There are few more possibilities. Every possible HTTP status code can be put into one of the five group
    - Informational responses (100-199)
    - Successful responses (200-299)
    - Redirect errors (300-399)
    - Client errors (400-499)
    - Server errors (500-599)

For a reference documentation on status code see [here](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)

Before we move on, we can solidify what we learned and look at a request and response in action by heading to the developer console in Google Chrome. From there, go to the Network tab and see the flow of HTTP request and responses activities while browsing a web page.