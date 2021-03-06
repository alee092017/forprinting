# ExpressJS

### Learning Objectives

- Describe what Express.js is and why we need it
- Introduce the concept of MVC
- Interact with HTTP verbs
- Identify the HTTP verbs we'll be using for an API

### RECAP: Node and NPM!

- Node is a low-level, non-blocking, event-driven platform which allows you to write JavaScript outside of the browser, on the server-side.
- NPM is Node's package manager. It's used to manage dependencies. It's the largest collection of open source libraries in the world.

## What is Express.js?

Express.js is a simple web framework for Node.js, a skeleton support to build applications in Node. It favors _configuration_ over _convention_, which means it's very unopinionated about how you build your apps and what structure you use to set them up.

Its biggest highlights are:
- Extremely lightweight / minimalistic
- A thin layer on top of the Node.js platform that helps manage the server and routing
- Its robust API allows you to easily configure routes. These routes can receive requests from the frontend, connect to a database, and send back the data requested.

We will be following the MVC pattern to build our express apps.

### What is MVC?

MVC is a software design pattern for building DRY, modular applications and systems.

- The **model** represents the application data. It deals with the part of the app that needs to talk to the database.
- The **view** is what the user sees in the browser. It displays the data from the database in a way that makes sense for the site's design.
- The **controller** handles the requests made from the browser and connects them to the correct parts of the model. It handles input and converts it to actionable items for the model or view.

The MVC pattern is just one of many patterns that can be used to create an Express app. It's similar to the pattern that Rails uses.

### PREVIEW: An Express app's file structure

We're going to work up to having this full file structure over the next few lectures, but I just wanted to introduce it now, so that it's not a shock later on.

When we build larger apps, it's important to have something called "separation of concerns". This means that each file contains functionality specific to one particular feature of the app. Using a modular approach has a number of benefits:
- It's easier for developers to work together if each developer can work on one file instead of every developer working in the same file.
- It's easier to find the functionality you're looking for. If you know all the files that contain your routes live in the same spot, then if you're having a problem with your routes or want to add new routes, it's much easier to find what you're looking for.

This is the structure for a flashcards app. We'll be building this out in class together over the next week.

```bash
├── README.md
├── server.js
├── controllers
│   └── flashcards-controller.js
├── db
│   ├── config.js
│   ├── migrations
│       └── [stuff will go here]
│   └── seeds
│       └── seed.sql
├── models
│   └── Flashcard.js
├── package.json
├── public
│   ├── index.html [sometimes this is omitted]
│   ├── src
│   │   └── main.js
│   └── styles
│       ├── reset.css
│       └── style.css
├── routes
│   └── flashcards-routes.js
└── views
    └── flashcards
        ├── flashcards-edit.ejs
        ├── flashcards-index.ejs
        ├── flashcards-new.ejs
        └── flashcards-show.ejs
```

# Let's start building an app!

(Don't do this along with me; just watch me for now.)

### Part 1: Setup

1. In this directory, make a new folder: `mkdir thundercats-flashcards`. Then cd into it and `touch server.js`.
2. Create a `package.json` file using `npm init`.
3. Add Express as a dependency -- `npm install --save express`

Open up the directory in the editor and check out the `package.json` file. You should see this:

```json
"dependencies": {
  "express": "^4.15.3"
}
```

### Part 2: Setting up our app

In `app.js`:

```js

// import express from our dependencies
const express = require('express');
// initialize the app
const app = express();
// set the port, either from an environmental variable or manually
const port = process.env.PORT || 3000;


// tell the app where to serve
app.listen(port, () => {
    console.log(`Listening on port ${port}`);
});

```

Now, we can go back in our terminal and run the app file by typing `node server.js`. Then, when we visit `localhost:3000` in the browser, we can see that our app is running! (You can quit the server by pressing `ctrl+c`.)

### DETOUR: Start scripts!

Instead of typing `node app.js` every time you want to run the app, it's much more convenient to add a script to your `package.json`.  We'll be adding three scripts:

```json
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "node server.js",
    "dev": "nodemon server.js",
    "debugger": "DEBUG=*:* npm run dev"
  }
```

- `test` was provided for us by `npm init`.
- `start` is the base way to start the app. We can run it with `npm start`.
- `dev` is the script we want to use in the development environment. Run with `npm run dev`
- `debugger` provides us with a lot of extra information if we're troubleshooting. Run with `npm run debugger`

In order to run the `dev` and `debugger` scripts, we need to install a new NPM package -- nodemon. To do this, run `npm install -g nodemon`. (The `-g` flag means "global" -- you can reference that package anywhere.)

### Part 3: Hello world!

Right now, we have an app running, but it really doesn't do much yet. Let's add a route that says "Hello World".

In `server.js`:

```js
// index route
app.get('/', (req, res) => {
    res.send('Hello world!');
});
```

This is another method on the app object. It describes a `GET` request to the root route of the app.

- It takes a string and a callback
- The callback takes two arguments, `req` and `res`.
    - `req` stands for the request object received from the browser
    - `res` stands for the response object that will be sent back to the browser.
- Within the callback, we access a method on the response object in order to send 'Hello world!'.

Now, when we run `npm run dev` and visit `localhost:3000`, we see 'Hello world!' rendered in the browser.

#### [FROM THE EXPRESS DOCS](https://expressjs.com/en/starter/basic-routing.html):

Route definitions always take the same basic structure:

```js
app.METHOD(PATH, HANDLER);
```

Where:
- `app` is an instance of express
- `METHOD` is an HTTP request method, in lowercase
- `PATH` is is a path on the server
- `HANDLER` is the function executed when the path matches.

We'll talk about routes more tomorrow 😉

### Part 4: Adding a logger

This part isn't strictly necessary, but it definitely helps us see what's going on with our Express app.

- `npm install --save morgan`

In `server.js`:

```js
// put this with the rest of your dependencies
const logger = require('morgan');
// we don't need this yet but might as well add it now
const path = require('path');
// put this right above the port
app.use(logger('dev'));
```

Now, we can see in the console every request that's made and how long the response took.

This is something called _middleware_, which is a fancy term for stuff that happens in between when the request hits the server and the route handler completes the request/response cycle. We'll dive deeper into middleware next week. 

## 🚀 Lab!

Follow the steps above to create your own "Hello world!" mini-app! 

### 🚨 DO NOT COPY AND PASTE! 🚨

The more times you type something out, the more likely you are to understand it. _This is proven science._

# A Few More Routes

We're going to do a deep dive into express routes tomorrow, but for now, let's add a couple more basic routes to our flashcards app so far

Of course, we want to do more with our app than just saying "hello world". We can do this by adding more routes to our app.

### Add an error handler

```js
// get anything that hasn't already been matched
app.use('*', (req, res) => {
    // set the status as 404 and send a response
    res.status(404).send({
      error: 'Not Found',
    });
});
```

Now, instead of saying "CANNOT `/GET`" on all the routes we haven't set up, it'll send back an error instead.

***Order matters here.*** If we put our error handler above our root route, we won't ever be able to get to it, since the `'*'` catches the request before it gets to the `'/'`. 

### Add our first additional route

Since this is a flashcards app, we need to have a route that gives us some code flashcards!

```js
app.get('/flashcards', (req, res) => {
  res.send('Flashcards!');
});
```

But let's say we wanted to send back JSON data, for example, instead of just plain text. We can change `res.send` to `res.json`:


```js
app.get('/flashcards', (req, res) => {
  res.json({
    message: 'ok',
    data: {
      flashcards: [
        {
          id: 1,
          question: 'What command do you use to make a new file?',
          answer: 'touch [filename]',
          category: 'CLI',
          difficulty: '1',
        },
        {
          id: 2,
          question: 'What are the primitive JavaScript datatypes?',
          answer: 'string, number, boolean, null, undefined, symbol (new in ES6)',
          category: 'JS Basics',
          difficulty: '1',
        },
        {
          id: 3,
          question: 'What is the difference between null and undefined?',
          answer:
            'Null is set specifically when there is no value on purpose while undefined means the value has not been set or defined yet.',
          category: 'JS Basics',
          difficulty: '2',
        },
      ],
    },
  });
});
```

We can also put our data into a separate file and import it, using `module.exports`:

```js
const flashcards = require('./db/flashcards');
app.get('/flashcards', (req, res) => {
  res.json({
    message: 'ok',
    data: {
      flashcards: flashcards,
    },
  });
});
```

### Final step: serving an index page

Instead of just sending back json data, or "Hello World", it would be kind of nice if our app sent back an actual index page. Here's how we can set that up:

We first add a new directory called `public` and create an `index.html`. 

Then, we need to tell our `app.js` where to look for static files.

```js
// directly beneath where we set up the logger
app.use(express.static('public'));
```

Finally, we tell our root route to send the `index.html` file 

```js
app.get('/', (req, res) => {
  res.sendFile(path.join(__dirname, 'public', 'index.html'));
});
```

Now we can add JavaScript to that HTML file to make requests to our backend.

## 🚀 Lab!

Catch up in the app you've been working in. Once again, no copy-pasting!

# Recap!

- Express is a web application framework for Node.
- It allows us to build RESTful APIs and web apps in JavaScript all the way down.
- We control what data is sent back at which endpoint using routes.
- Following the MVC pattern allows us to create our apps in a modular way.


## 🚀 Lab!

Check out [this repo](https://git.generalassemb.ly/wdi-nyc-delorean/LAB_U02_D05_Candy-Routes) and follow the instructions you find there!
