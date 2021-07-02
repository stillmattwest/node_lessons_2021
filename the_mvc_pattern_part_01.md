# The MVC Pattern Part 1 - Controllers and Models

## Roadmap

1. What is the MVC Pattern?
2. Controllers
3. Connecting Controllers to Routes
4. Models
5. Connecting Models to Controllers
6. Summary

## What is the MVC Pattern?

As you're probably starting to realize, back-end code can get complex. A production app can have dozens or hundreds of routes and thousands of lines of code. 

The only way to stop your applications from being crushed into spaghetti by all that complexity is to give it structure.

Structure is a little different than something more "concrete" like programming syntax. Structure can be whatever you decide, or whatever your team decides. It's just a strategy that guides you in deciding where to put code, or how to break code up into smaller, manageable chunks. 

Thankfully, there are *design patterns* that can help you come up with a good structure that is easy to explain to others who may want to work on your project.

One of the most common of these design patterns is the MVC pattern. MVC stands for **Model - View - Controller**

**Models** are code that represents key components of our application. For example, in a blog you might create a model that represents a post, or a comment, or a user. Models are usually the part of your code that talks to the **database**.

**Views** contain code that the user can see. In traditional MVC applications, a **view engine** helps to transform dynamic code into HTML to display on the page. In more modern applications, the view layer is handled by full-featured frameworks and libraries like **React**. In our blog application, we will use a traditional view engine.

**Controllers** form the middle layer between the user and your models. Controllers make requests of **models** and select **views** to return to the browser.

Now that you understand the basic concepts of the MVC Pattern, let's put them to use. As usual, we're going to take baby steps in order to build confidence in what we're doing.

## Controllers

The first thing to understand about controllers is that they are just JavaScript functions. We're going to put them in a folder called controllers, but that is just a convention. We could called the folder pizza if we wanted to and it would still work.

At the root of your application, create a directory called **controllers**.

Inside that directory, create a file called **myFirstController.js**.
 
Your application structure should now look like this:

```bash
-- MyBlog
    -- controllers
        - myFirstController.js
    -- node_modules
        - lots of folders
    -- public
        - client.js
        - home.html
        - style.css
    - index.js
    - package-lock.json
    - package.json
```

In your **myFirstController.js** file, add the following code:

```js
exports.firstController = (req,res) => {
    res.send("Hello from firstController!");
}

```
Thre are only a couple of lines of code in that controller, but they represent some very important things to understand.

**Exports** is part of Node's module system. It is the other side of the **require** command that we've been using in our *index.js* file. Export and require allows us to turn our files into **modules**. Modules are what allow us to break our code up into small, maintainable chunks. Obviously, they are a critical part of Node. 

>Fun Fact: Node takes it's module system from the **common.js** standard, which basically just defines patterns for how JavaScript modules should work.

There are a couple of ways to do exports. Sometimes you will see them look like this:

```js
firstController = (req,res) => {
    // do stuff
}

export default firstController;
```

The difference between that syntax and the syntax we're using is that ours allows us to export multiple controllers from within the same file. Both are valid exports. 

Now that we've created our first controller as a module, let's attach it to a route.

The first thing you need to do is to **require** your new controller in *index.js*. 

In *index.js*, add the following line to the top of your file, near your other require statements:

```js
const express = require("express");
const app = express();
const port = 3000;
// controllers
const myFirstController = require('./controllers/myFirstController')
```

Now all we need is a new route. We'll pass our controller in as the routes callback.

In *index.js*, down by your other routes, add the following line:

```js
app.get("/firstController",myFirstController.firstController);
```
That's it! You've just created your first controller and attached it to a route. 

Your index.js file should now look something like this:

```js
const express = require("express");
const app = express();
const port = 3000;
// controllers
const myFirstController = require('./controllers/myFirstController')

app.use(express.static(__dirname + "/public"));

app.get("/home", (req, res) => res.sendFile(__dirname + "/public/home.html"));

app.get("/firstController",myFirstController.firstController);

app.get("/", (req, res) => res.send("Hello Express!"));

app.listen(port, () => console.log(`listening on port ${port}.`));
```

Note how we are referencing firstController. We import the file that it is in, and then we call the specific function we're looking for (firstController) using dot syntax. 

To test it, start up your application and navigate to http://localhost:3000/firstcontroller.

Currently, our controller is just returning a simple message. Their true role in MVC is threefold:

1. Receive user requests
2. Call any models necessary to get the data the user needs
3. Send a response to the user

We'll get there soon, but first we need to learn about **models**

## Models

Models are the central piece of the MVC pattern. Models are where you will keep most of your application logic.

For now, let's just create a model and get it talking to our controller.

In the root of your application, create a directory called *models*. 

Inside that directory, create a file called *myFirstModel.js*.

Your application structure should now look like this:

```bash
-- MyBlog
    -- controllers
        - myFirstController.js
    -- models
        - myFirstModel.js
    -- node_modules
        - lots of folders
    -- public
        - client.js
        - home.html
        - style.css
    - index.js
    - package-lock.json
    - package.json
```

Inside *myFirstModel.js* add the following code:

```js
exports.firstModel = () => {
    return 'Hello from first model!';
}
```

Then, let's reference our model inside our controller. Change the code in *myFirstController.js* to this:

```js
const myFirstModel = require('../models/myFirstModel');

exports.firstController = (req,res) => {
    const response = myFirstModel.firstModel();
    res.send(`Hello from firstController! I called      firstModel and it says: ${response}`);
}
```
As you can see, models and controllers are just JavaScript functions. We conncect them using Node's **exports** and **require** statements.

This is a really important thing to understand about MVC. It's not a framework or something that has a specific syntax. It's just a strategy for keeping our code organized and maintainable. 

Go ahead and navigate to http://localhost:3000/firstController to see proof that our controller and model are talking to each other. 

In our next lesson, we'll take a long look at the final part of MVC: the **view**.





