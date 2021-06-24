# How to Learn Node.js and Express.js  Part One: Basic Node

## Overview

In this lesson, you'll learn what Node is, and how to use it to create a simple web server. You'll learn about how web servers work, and about the Node Request and Response objects.

## Roadmap
1. Use Node to run a JavaScript file
2. Create a Simple Web Server
3. Learn how Web Servers Work
4. Learn about the Request and Response Objects

## What is Node?

Node.js is a **JavaScript runtime environment**. JavaScript is an interpreted language, which means that without a program to interpret it, a JavaScript file is just a text file. In front-end development, we execute JavaScript code in the interpreter provided by the broswer. 

Node.js allows us to run JavaScript *outside* the browser. It also contains modules that are useful for creating a web server.

Throughout this project, we will be referencing documentation from the node website.

https://nodejs.org/en/docs

## Create a New Node Project

In these tutorials we will be creating the back-end of a blog called MyBlog. We'll build it up step-by-step, starting from basic concepts. Follow the steps below to get started.

1. Create a directory called MyBlog
2. Open the MyBlog directory in your code editor
3. Create a file called test.js

Let's add some basic JavaScript to test.js

```js
console.log('Hello Node');
```

Now open a terminal in your MyBlog directory. Type in the following command:

```bash
node test.js
```

You should see **Hello Node** output to console (terminal). If you get an error you might not have Node installed, or you might be in the wrong directory, or you might have named your file something other than test.js.

Let's modify our test.js file

```js
function greeting(name){
    return `Hello ${name}, you have executed a function in Node.js`
}

console.log(greeting('Matt'));
```
Go ahead and run your file again using the **node** command. You should see the expected output. 

By now, I'm sure you have the idea. Node allows us to run JavaScript files without a browser. Awesome!

Let's move on.

## Create a Simple Web Server

1. Delete your test.js file
2. Create a new file called index.js

Now its time to create our Node application. Although we learned in the last section that Node can run JavaScript files with any name, in this case the name **index** is important. By default, Node will look for a file called index when it first opens a project.

Technically, it looks for some other things first, but we'll get to that later.

Add the following code to index.js. Don't worry, we'll go over what all this code does in just a little while.

```js
const http = require("http");

http
  .createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Hello World!");
  })
  .listen(3000);
```

## Run the application

You've already learned that Node can execute JavaScript files, but that's not all it does. Node also provides functionality that allows for things like listening for HTTP requests and accessing the local file system, things that are impossible in a browser environment for security reasons.

In your terminal, run the following command.

```
node index.js
```

What you should see is... nothing. But if you look closely, you'll notice that you don't have a command prompt in your terminal. This is because your node app is still running!

## Test the application

Open a browser and direct it to _http://localhost:3000_ The '3000' indicates the _port_ we are listening on. Normally, webpages use port 8080 but for development purposes we are using port 3000. We specified this port in our application with the **.listen** method.

If everything is working, you should see the text 'Hello World' in your browser. Congrats!

## About web servers

So, our application works. But what is it doing?

All web applications have to run on a _server_. In our case, our server is on our local computer, running on port 3000. This is why we have to access it with the url of **localhost**. We could also find it by its IP adress of **127.0.0.1** which is the standard IP address for the localhost. Go ahead and try it. With your node application still running, direct your browser to _http://127.0.0.1:3000_. You should still see 'hello world.'

Now go back to your terminal and press **ctrl-c**. This will stop your server and you should see a command prompt appear in the terminal.

Now go refresh the page in your browser window. You should see an error page because we just shut our server off.

## The Node HTTP Module and the Require keyword

Node applications draw much of their functionality from _modules_. Modules are basically JavaScript libraries that provide _boilerplate_ code. Code is referred to as boilerplate when it just handles the bording, road-grating kind of problems that pretty much all web applications need to run. 

For instance, all web applications have to be able to listen for and respond to HTTP requests. Rather than having to write this functionality ourselves, Node allows us to import it in a module so we can focus on the real value of our web application.

Basic http functionality is brought in via the Node _http module_ which is installed along with Node. We bring that code into our application via the _reqire_ keyword.

The *require* keyword is a big deal. It allows us to reference code in other files.

We can even bring in code that we write in this way, allowing us to break our application up from one massive file into many smaller files. This obviously makes it much easier to find and understand the various parts of our application.

You can learn more about the Node Http module here: https://nodejs.org/api/http.html

## HTTP Request and Response

Let's take a closer look at another part of our application.

```js
 .createServer((req, res) => {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Hello World!");
  })
```

The **.createServer** method handles the low level tasks of creating a new web server instance, including binding our new server to an address and listening at a specified port. We are passing createServer a _callback_, which is a function passed as a parameter. In this case, we're using an _anonymous function_ which is simply a single-use function that we don't bother to name. If we look a little closer, we can see that function is being handed two parameters, **req** and **res**. But what are these parameters? Where did they come from?

We'll get to **req** and **res** in a bit. For now let's just look at what **createServer** and the callback are doing.

The callback function we pass to createServer up above is an _event handler_ that responds to the _event_ generated when our server detects an incoming HTTP request. Our callback is thus called _every single time_ an HTTP request comes in. We can test this by adding a console.log statement to our code.

```js
.createServer(function(req, res) {
    console.log('Heads up! Incoming HTTP request!');
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Hello World!");
  })
```

Go ahead and add that line of code to your application and run _node index.js_ in your terminal again. Then go to your browser and direct it to your app and hit refresh a few times. Then come back to your terminal and see what is there.

What you should see is two logs for every time you hit the page. Why two? If you use the network tab of your Chrome Dev Tools you'll see that Chrome is actually making two HTTP requests of your app. One is for the base request and the other is looking for a fav icon (fav.ico). Both of those are HTTP requests, so our application fires its callback twice.

Let's add a bit to our initial application. Let's add a variable to store the port number we want to use so we can change it easily if we want to.

Server.listen (createServer creates an instance of a Node server) also takes a callback after the port. Let's give it a console.log to give us some feedback about when our server is running.

Our new application should look something like this:

```javascript
const http = require("http");
const port = 3000;

http
  .createServer(function(req, res) {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Hello World!");
  })
  .listen(3000, console.log(`Listening on port ${port}`));
```

Go ahead and restart your application by pressing _ctrl-c_ and then running the command _node index.js_ in your terminal.

Now that we understand more of what **createServer** and our callback is doing, let's go ahead and remove that _console.log_ line so it isn't cluttering up our terminal with every request.

## The Request and Response Objects

The callback we're handing **.createServer** takes two parameters, **req** and **res**. These objects are generated automatically whenever a _request event_ is detected, and they are passed to our callback function.

The **request object** is generated by the http module (an http.incomingMessage object.) It gives us access to response status, headers, and data in the incoming request.

The **response object** is also generated by the http module (an http.serverResponse object). It allows us to shape the http response our server will send back to the client. You can see two of this object's methods in our existing application in **res.writeHead** and **res.end**.

You can learn more about the methods available to request and response at https://nodejs.dev/.

### Working with Request (req) object

Let's see what the request object contains. Modify your **.createServer** event handler like so:

```javascript
http
  .createServer(function(req, res) {
    console.log(`URL from req: ${req.url}`);
    res.writeHead(200, { "Content-Type": "text/html" });
    res.end("Hello World!");
  })
  .listen(3000, console.log(`Listening on port ${port}`));
```

You'll need to restart your application to see the new log.

Try directing your brower to _http://localhost/hello-request-object_ and see what happens.

You should see the new url (the part after the hostname and "/".) You should also see that fav icon request more explicitly.

There is a lot more to the request object of course, especially as we bring in new modules to work with it.

### Working with the Response (res) object

Let's modify our request handler to send that url data back to the client. Modify your request handler like so:

```javascript
http
  .createServer(function(req, res) {
    res.writeHead(200, { "Content-Type": "text/html" });
    res.write(`req.url: ${req.url}`);
    res.end("Hello World!");
  })
  .listen(3000, console.log(`Listening on port ${port}`));
```

There is more we can do with req and res but in reality Node isn't used as a standalone webserver because that would require developers to write a lot of boilerplate code.

Rather than doing that, we're going to bring in one of the most important Node modules: **Express.js**.

## FAQ

_Q: Wait a minute, if this isn't really how node web servers are used, why did we just learn it?_

A: While it's true that Node webservers are usually built using modules like Express, it's important to understand what is going on under the hood. An Express app _looks_ different than one in pure Node, but it's still running Node methods underneath.

_Q: All we returned was text. How do we get our servers to return HTML, CSS, etc?_

A: We're getting there, don't worry.