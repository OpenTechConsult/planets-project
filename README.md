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