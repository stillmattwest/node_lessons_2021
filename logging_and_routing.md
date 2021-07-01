# Logging, Debugging, and Requests

## Roadmap

## Intro

Right now we have a basic web server that can serve static files and respond to a couple of basic routes. In this lesson we're going to learn how to use logging and debugging to gain insight into what our application is doing at each step and we'll install a few new modules to help us do this with very little code.

## Logging With Morgan

Morgan is a fantastic little logging utility that logs information about every request and response that comes in and out of your web application. It can also be configured to log to a file but we're going to just use the console logs here.

Run the following command to install Morgan:

```
npm install morgan --save
```

Now let's bring it into our app with the _require_ command. Edit the top few lines of your application to look like this:

```javascript
const express = require("express");
const app = express();
const morgan = require("morgan");
const port = 3000;
```

In a previous lesson we learned about **middleware** and about how Express apps are really just a big stack of middleware with some web functionality to hold it all together. You may have guessed by now that morgan is brought in as middleware.

Remember the flow of an Express application:

```
Clent Request => middleware stack => server response
```

All middlewares have access to the request and response objects and can change them. They can also end the request and response cycle by something like **res.end**. Morgan, however, doesn't alter anything, it just logs to the console.

Add Morgan to your application as middleware. Edit your application like so:

```javascript
const express = require("express");
const app = express();
const morgan = require("morgan");
const port = 3000;

app.use(morgan("dev"));

app.use(express.static(__dirname + "/public"));

app.get("/home", (req, res) => res.sendFile(__dirname + "/public/home.html"));

app.get("/", (req, res) => res.send("Hello Express!"));

app.listen(port, () => console.log(`listening on port ${port}.`));
```

If you already have your application running, Nodemon should automatically restart to incorporate your changes. If you don't have it running, go ahead and start it now by entering _npm start_ in your terminal.

Open your application in your browser and refresh a few times. Back in your terminal, you should see lines that look something like this:

```javascript
GET /home 200 11.690 ms - 291
GET /style.css 200 2.906 ms - 235
GET /client.js 200 2.070 ms - 39
```

If you haven't cleared your cache recently you might see **304** responses instead of **200**. An HTTP 304 response just means the client already has the resource cached, so no change is necessary. It's basically a redirect to a locally cached resource.

Let's look at where we bring Morgan in to our application. There are two lines.

```javascript
const morgan = require("morgan");
...
app.use(morgan("dev"));
```

The _require_ statement allows us to reference the Morgan node module in our index.js file. Without this, 'morgan' would be undefined.

**app.use** allows us to bring in an application as a middleware. We pass our 'morgan' variable to this method, along with the configuration parameter of 'dev', which specifies a certain format of logging output (see the Morgan docs).

Morgan is very customizable but we won't get into that here. If you want to learn more check out the documentation here: https://www.npmjs.com/package/morgan

## Routes

Even a simple web application needs to deal with different types of client requests. For example, our blog application will need to handle requests for fetching posts, fetching comments, fetching individual posts by their ID numbers, and fetching the comments associated with that post. 

It is easy to imagine even more functionality for a blog. We might need to handle user logins, password changes, log outs, and functionality for creating new posts or editing existing ones.

On the front-end, we address these requests to different URLs. For example, we might send a requests for all posts to https://myblog.com/fetch-all-posts. A request for an individual post might look like https://myblog.com/fetch-post/345.

Your application will need to listen for these URLs and *route* (send) them to different functions for processing.

For now, let's just focus on the routing part.

These are the routes we have so far in our app:

```js
app.get("/",(req,res)=> {
    res.send("Hello Express!");
});

app.get("/home", (req, res) => res.sendFile(__dirname + "/public/home.html"));

```

Express's **get** method assigns routes that come in via an HTTP GET request. There are other types of HTTP requests including **post**, **put**, and **delete** but we'll deal with those later. **Get** is the default type of request, and it is meant to retreive data from a server, such as an HTML or CSS file.

**get** takes two parameters, a **route** and a **callback**. The route lets Express point a request to a certain function. The callback is the function in question. The callback is passed the **req** and **res** objects by default.

In the example above, when we get a request with the "/home" route, Express will go down the middleware stack from top to bottom. If it runs into an **app.use** it will apply the middleware regardless of route. This is how we implement logging.

If it runs into an **app.get** it will only apply the middleware if the route in the request matches the route specified in the method. This is why a request to *http://localhost:3000* does not get served our static HTML file, but a request to *http://localhost:3000/home* does.

When a request matches a route, the callback is fired. In the case of our "/home" route, the callback is just the **res.sendFile** method.

It would be possible to put all the functionality our application needs right inside the routes, but Express applications should not be designed this way. In fact, routes should rarely be more than a single line of code. 

We'll learn more about the proper way to structure an application in the next segment.
