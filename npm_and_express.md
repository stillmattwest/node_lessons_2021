# NPM and Express

## Roadmap

1. NPM and NPM Init
2. Add Express
3. Initialize Git
4. Using Express
5. Nodemon
6. Serving Static Files
7. Middleware
8. Summary

## NPM and NPM Init

In a previous lesson we learned how to create a (very) basic web server using nothing but Node and its built-in http module. Now, we're going to bring in a couple of outside modules, which you'll remember are JavaScript libraries that add functionality to our application.

The most popular module for adding web server functionality to Node apps is **Express.js**. We're going to bring that in but before we can do that we need to learn a bit about the **Node Package Manager** or NPM.

NPM is an online repository with hundreds of thousands of node modules. We can bring them into our application as needed via a simple command line utility.

You already have NPM on your system, it installed alongside Node (cavet: some Linux users may have to install it separately with `apt install npm`).

### NPM Init

Modules installed via NPM live in a directory called **node_modules**. Because this directory can get very large, you should add it to your .gitignore file. We'll do this later after we add version control to our project.

Node Modules that are required for your application to run are referred to as _dependencies_. A reference to these dependencies is kept in a file called **package.json**. The modules referenced there are installed via the **npm install** command.

What _npm install_ does is to look at your package.json and read the references to the dependencies you need. It then goes out to the Internet automatically and downloads and installs everything.

This is a great system that allows node applications to be highly sharable and version-control friendly. Before we can use this system, we'll need to get our application set up for it with the **npm init** command.

Make sure you're in your MyBlog directory and run the following command in your terminal:

```
npm init
```

This will initialize package.json in your system. The utility will prompt you with a bunch of questions. Just accept the defaults for now, only add your name as the author of the app.

You should now see a file called **package.json** in your directory. Go ahead and open it up and look around but don't change anything.

## Add Express

We can now add Express to our application. In your terminal, run the following command:

```
npm install express --save
```

This command will cause npm to find the **express** module and add it to your application as a dependency.

**NOTE:** The _--save_ option is actually unnecessary in modern version of npm as new modules are saved as dependencies by default. However, it is listed in a lot of online tutorials and so is included here to avoid confusion.

After you run the command you should see a new folder in your application: **node_modules**.

Go ahead and open up node modules and look around. You'll notice that there are a lot of files in there. Each of these folders contains a small application that is a _dependency_ of Express, which is in turn a dependency of our application (or at least it will be very soon.)

If you look in your **package.json** you will see that it has changed, as well. Express is now listed as a dependency.

There is also another file that npm added to our project: **package-lock.json**. Package-lock.json keeps a more exact dependency tree for your application and locks the version needed for each of these dependencies. _It does not replace package.json_. Keep both in your application.

If you look inside package.json you will notice that it includes entries for a lot of different modules. These files and folders are in your node_modules directory and they are the same dependencies of Express we looked at earlier.

Your file structure should now look like this:

```bash
MyBlog
    - node_modules
        - a bunch of dependency files
    -index.js
    -package-lock.json
    -package.json
```

## Initialize Git

Go ahead and go to your Github page and add a new respository for MyBlog, making it private or public as you prefer. Then, come back and initialize Git in your MyBlog directory. This will allow you to recover if there is a problem while building this app.

But wait! Look at the number of changes! This is because of our node_modules folder.

Create a .gitignore file and add node_modules to it. Once that is working go ahead and make a commit and push it to your new repository.

## Using Express

Since Express is a module specifically to bring in web server functionality, all of our previous code in **index.js** can now be improved. Go ahead and delete it and replace it with the following:

```javascript
const express = require("express");
const app = express();
const port = 3000;

app.get("/", (req, res) => res.send("Hello Express!"));

app.listen(port, () => console.log(`listening on port ${port}.`));
```

Go ahead and restart your node application and direct your browser to http://localhost:3000 again. You should see the new output.

Obviously, this application has similar functionality to our vanilla Node version but let's take a look at it anyway.

At the top of the app we're creating a new variable that brings in Express via the **require** statement. Node knows to look in **node_modules** for this file by default. (it's in _node_modules/express/lib/express.js_ so there's some shorthand going on here that Node knows how to support.)

After that we're creating another variable called **app** that is set to the result of _calling_ our express variable. Calling express returns an object, similar to instantiating a class. The end result is that **app** now has all the functionality of an express application, and we'll be using it extensively.

**app.get** is setting up an express _route_, which is the url, or path, that a request can come in at. Routing is an important topic in web development so let's take a closer look.

```javascript
app.get("/", (req, res) => res.send("Hello Express!"));
```

**get** is obviously a method because we're passing it parameters. The first parameter is the URL we want to match. "/" is _default route_ like _http://localhost:3000/_. This route will _only_ repond to http _get requests_ (more on http verbs later.)

After the URL parameter, we see the **req** and **res** objects we covered in a previous lesson. Express gives us deeper access to these objects (especially req) but they're the same objects and they come from the same source (the Node http module, and the request event.) We're then using an arrow function to declare an event handler, which just sends a simple response back via the send method of the **res** object.

**app.listen** returns a Node http module server instance, which we've used before.

## Nodemon - A very useful module

Right now, we have to stop and restart our server every time we make a change. That is going to get really annoying really fast so let's solve the problem with a module called **nodemon**. Nodemon will monitor our running application and if it detects a change it will stop and restart it automatically. Nice!

In order to use nodemon at the command line we'll need to install it globally with the -g option. Run the following command in your terminal:

```
npm install nodemon -g
```

Now restart your application with this command:

```
nodemon index.js
```

## NPM start

A common way to start Node applications is to create a _start script_ and save it to **package.json**. Let's do this for nodemon.

Open your package.json and edit the "scripts" portion like so:

```
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "nodemon index.js"
  },
```

You'll notice that your application detects this change and automatically stops and restarts. Thanks, nodemon! However, let's go ahead and manually stop it with **ctrl-c** and then enter the command _npm start_ in the terminal. Your application should start under nodemon once again. **npm start** will be the best way to start your application from now on.

It is possible to store and run a wide variety of script commands in package.json in similar fashion.

## Serving Static Files

So far, all we've done is return text from our server. Let's use Express to send an actual webpage, instead.

In your MyBlog directory, create a new folder called **public**. This will hold any static files we need, such as css files and client-side JavaScript. We'll put a static HTML page in there for now, too.

In your public folder, create a file called **home.html**. Add the following code to it:

```html
<head>
  <title>My Blog</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <header>
    <h1>Welcome to My Blog</h1>
  </header>
  <div class="container">
    <p>Let's learn about Node, Express, and SQL</p>
  </div>
  <script src="client.js"></script>
</body>
```

Now create a file called **style.css** and add the following code.

```css
html,
body {
  margin: 0;
  border: 0;
  padding: 0;
}
header {
  margin-top: -10px;
  padding: 20px;
  background-color: #f2f2f0;
  font-size: 48px;
  height: 200px;
}

.container {
  width: 75%;
  margin: 0 auto;
}
```

Last, create a file called **client.js** and add the following code:

```javascript
console.log("Hello from client.js!");
```

Save all your files.

But how to get to these files? In a front-end application, the browser knows to look for a file called _index.html_ but this is a Node application, which works with http requests. How do we tell the browser to load a static file?

We have to do two things: one, we have to tell our application to serve static files and the directory to find them in, and we have to create a _route_ that will point to home.html.

## Express.static

Express.static is a method that allows our application to serve static files. It is a type of _middleware_. Middleware is a big concept in React and we'll talk more about it later.

For now, edit the application to look like this:

```javascript
const express = require("express");
const app = express();
const port = 3000;

app.use(express.static(__dirname + "/public"));

app.get("/", (req, res) => res.send("Hello Express!"));

app.listen(port, () => console.log(`listening on port ${port}.`));
```

You can learn more about Express.static here: https://expressjs.com/en/starter/static-files.html.

If you follow that link you'll see that Express.static should serve static files that are appended to our site URL after a "/". For example: _http://localhost:3000/home.html_.

Go ahead and try it. What happens?

You should see your static HTML page. It should be styled with CSS and if you check your Chrome Dev Tools you should see your message from **client.js**. (client-side JavaScript will still log to the browser console, not the Node console.)

Open your network tab in dev tools and refresh the page. You'll see able to see the HTTP traffic that requests not only your HTML page but the CSS and JavaScript pages, as well. (If you don't see it, try clearing your browser cache.)

But what happens if we direct our browser to \*http://localhost:3000/home"? IE, without the **.html** extension?

```
Cannot GET /home
```

This error happened because without the file extension, Express doesn't have a way to know that we aren't trying to enter the URL for a route called "home".

We can solve this, though. We just need to add a route.

Edit your **app.js** file like so:

```javascript
const express = require("express");
const app = express();
const port = 3000;

app.use(express.static(__dirname + "/public"));

app.get("/home", (req, res) => res.sendFile(__dirname + "/public/home.html"));

app.get("/", (req, res) => res.send("Hello Express!"));

app.listen(port, () => console.log(`listening on port ${port}.`));
```

Try refreshing your browser. Nice! Our application is starting to seem more and more like a real web server.

## Middleware

So far, our app can listen for HTTP requests, serve static files, and send basic responses to two different routes. All of that functionality is thanks to the power of _middleware_.

Express is a self-described "minimalist" application and by itself it has, well, minimal functionality. An Express application is basically a big stack of middleware with some basic web services to tie it all together.

But what is middleware? Middleware is a stack of applications that sit between the _client request_ and the _server response._ Express allows middleware to do a few different things:

- Execute any code.
- Make changes to the request and the response objects.
- End the request-response cycle.
- Call the next middleware function in the stack.

In our application, we have three middlewars in our stack:

```javascript
app.use(express.static(__dirname + "/public"));

app.get("/home", (req, res) => res.sendFile(__dirname + "/public/home.html"));

app.get("/", (req, res) => res.send("Hello Express!"));
```

That's right, even the route handlers are middleware!

Middleware will pass through your app from top to bottom and will stop at every _matching_ route. app.use will affect everything, where the app.get middlewares will only affect a request that matches their route.

You can see how the route handlers use the req and res objects, as middleware is allowed to do. The route handlers also _end the request/response cycle_ by calling the **res.send** and **res.sendFile** methods, respectively.

There are many other types of middlewares and you will write more yourself for your own applications.

### A Note on \_\_dirname

**\_\_dirname** (note the two underscores) is a feature of Node. **\_\_dirname** is always equal to the directory of the current file.

## Summary

Our application has come a long way in a short time. We went from a single JavaScript file to a full application with dependencies, middleware, and a folder for static files. Our application can now respond to multiple routes with unique responses for each.

Coming up, we'll learn about a few more essential modules for Node applications and we'll learn about processing user data in our requests using either request parameters and form data. We'll need to understand all of that before we can start working with our database to have a legitimate full-stack application.