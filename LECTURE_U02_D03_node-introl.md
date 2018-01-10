# Intro to Node.js

## Learning Objectives
- Explain what Node.js is, what it does, and how it is used
- Use modules to organize and share code
- Describe the usage of NPM
- Introduce some common core modules
- Use export and require to share code between files

## What is Node.js? - Intro (20 mins)

Node.js is a platform for running JavaScript _outside_ of the browser. There have been programs for doing this since shortly after JavaScript was initially released in the mid-90s but it wasn't until 2009 when a developer named Ryan Dahl made a JavaScript platform using the super powerful Chrome V8 engine that the practice of server side JavaScript really took off.

<!-- While it's possible to build web applications and APIs in straight JS, we'll actually be using a framework on top of Node called Express. -->

### Language vs Runtime Environment

JavaScript is a *programming language*. It has a particular syntax defined in the [ECMAScript Standard](https://www.ecma-international.org/publications/standards/Ecma-262.htm) which specifies  keywords (`for`, `function`), syntax (`[]` for arrays, `{}` for objects or codeblocks), and built in functionality.

JavaScript, like most modern languages, is a "high-level" language which means we write JavaScript to be run by another program. This other program is called the "runtime environment". The runtime provides most of the functionality that we sometimes think of being "built in" to JavaScript itself. We've already seen one JavaScript runtime environment: the browser. It provides things like `window`, `document` and the DOM API that *are not* built in to the language, but are provided by the host.

The next runtime environment we're going to look at is `Node.js`. It's the same language, but in a *different* environment, so the syntax of the JavaScript we write is the same (e.g. functions and objects) but there is different functionality (e.g. no `window`, no `document` and instead we'll have a `process` object).

### Why are people excited about Node?

- It allows you to build fast, scalable APIs and sites in JavaScript (see **Asyncronous** below).
- It is nice to write full-stack applications in a single language.
  - If code is general enough, it can be used in the front and back end
  - It is pleasant not needing to switch between languages when hoping between the front and back end

## Asynchronous

![](https://s-media-cache-ak0.pinimg.com/736x/69/17/05/6917054d2af2eec807c25a376f72a485--starbucks-store-starbucks-coffee.jpg)

Imagine a large busy coffee shop. There is a line of people waiting to order, served one at a time. After anyone orders, they go to the counter and wait with others for their coffee, which could take some time to make, and the next person in line is allowed to put their order in. When anyone gets their coffee, they say thank you, and leave.

This is an *asynchronous* interaction. To understand what that means, lets imagine a *small* coffee shop. There is a line of people waiting to order, served one at a time. After anyone orders, the cashier waits until their coffee is finished until they serve the next guest. What do you think the effect would be?

![](http://thesmartlocal.com/images/easyblog_images/2351/Nostalgic%20Cafe/rwimage.jpg)

Node.js's approach to processes that *might take an unknown amount of time*, like requesting data from the network or writing a file to disk, is to start things right away and move on, then notify your code when the process if finished. This is accomplished using "callbacks" which are functions that JavaScript runs whenever a processing is finished. The alternative is to pause your entire program until the processing is finished (called *blocking*), which Node.js avoids by design.

Node's asyncronous model was somewhat novel relative to popular tools for making frameworks at the time but having just come from working with events in the browsers, we should have some comfort with the idea of writing a function to be called some time in the future if some particular event occurs. In the browser that event might have been a click or a page load, now we will be applying the same idea to network requests and database accesses.

The difference between Node's asyncronous paradigm and a more traditional blocking approach are difficult to identify without a point of reference. When we look at Ruby this distinction will be more clear but consider the following two snippets to be run in the browser:

```js
const name = prompt('What is your name?')
console.log(`Hello ${name}!`)
```

```js
const form = document.querySelector('form')
const input = document.querySelector('input#username')

function handleFormSubmit (evnt) {
  evnt.preventDefault()
  console.log(`Hello ${input.value}!`)
}

form.addEventListener('submit', handleFormSubmit)
```

### Turn and Talk (3 minutes)

- The second is a lot longer and more complicated -- what advantages does it have over the first?

### What the heck is the event loop anyway?

In every JavaScript runtime is something called an event loop and a callback queue. The idea is that instead of juggling when to do what, we tell the runtime environment that we want to associate certain behavior with certain events and the runtime handles kicking off the behavior at the right time.

#### In the Browser

![Browser Event Loop](http://prashantb.me/content/images/2017/01/js_runtime.png)

#### In Node

![Node Event Loop](https://i.stack.imgur.com/zYgcr.png)

An understanding of the event loop is not crucial to get started writing JavaScript (in fact, part of the selling point of JavaScript is that the complicated details of the event loop are hidden from the developer) but in order to be good developers, we want to understand what is going on at least one level below where we are working.

[Loupe](http://latentflip.com/loupe) is a tool for visualizing the event loop. Loupe was created by Philip Roberts to accompany an now famous talk he gave at JSConf in 2014 called "What the Heck is the Event Loop Anyway?". This video is worth watching and bookmarking to revisit as your understanding of JS grows.

### Why Choose Node/Express?

- Asynchronous means generally faster performance
- Better _concurrency_ – it can serve data to more users with fewer computer resources
- Designed to make real time applications (e.g. chat servers)
- JavaScript is everywhere; *one* language to rule them all

### Installing Node.js

To check if we already have Node installed, type: `node -v` in terminal. You will see the Node version if it's installed.

If it's not installed:

First, install Node Version Manager, run the following command:

`curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash`

Then we have to restart our terminal. Run the command `nvm help` to see if it was installed successfully. If it doesn't work, run the command `source ~/.nvm/nvm.sh`. Check again to see if it works.

To install node run the following command in your terminal" `nvm install node`.

After it finishes installing run `node` to test it out.

This will install both Node.js and npm on your machine.

*🎉 There are two ways to run JS in node – try them both (Code along):*

### Interactive Node (REPL)

  If you simply type node in terminal, you will launch Node's REPL (Read-Eval-Print-Loop) interactive utility.

  Let's test it:

```js
node

> 10 + 5
// 15

> const a = [ 1, 2, 3];
// undefined

> a.forEach(function(v) {
... console.log(v);
... });
// 1
// 2
// 3
```

Good for experiments, but not reusable.

Press control-c twice to exit REPL. [List of special REPL commands](https://nodejs.org/api/repl.html#repl_commands_and_special_keys)

### Executing a JS program

Write and execute some code in a file! In your `LECTURE_U02_D04_NODE_INTRO/practice/` working directory, create a file called `main.js` and write the following code:

```js
console.log('hello world!');
```

#### No Document

In a `node` REPL session, try the following:

```js
> document.querySelector("a")
> window
> document
```

What happens? Why?

#### New Toys

In a `node` REPL session, try the following:

```js
> process.cwd()
> process.env
> process.argv
```

## Node Modules - Built Ins

Like most other modern languages, Node is **modular**. It organizes its code into units called instead of depending on `<script>` tags. In general, this keeps things more organized and helps you write reusable code.

To practice with modules, lets try loading in some that are built into node. Try this in the node REPL

```js
> const os = require("os")
> os.platform()
> os.cpus()
> os.userInfo()
```

What results do you get? What do you think they mean?

`require` is node's way of allowing you to import another module, in this case the [OS module](https://nodejs.org/api/os.html) that is built into node. Note the syntax: require is a function that returns a value, in this case an object, that you interact with as you would any other JS object. `require` is very powerful, but it is not deeply special or different from JavaScript code we've already seen.

## Node Modules - Our Own

We're going to create our own `console.log` that writes to a file. In the practice folder create a file called `logger.js` and try the following:

1. import the [`fs`](https://nodejs.org/api/fs.html) module
2. write a function called `ourLog` that takes a string and writes it to a file called "log.txt" using the [`fs.appendFileSync` function](https://nodejs.org/api/fs.html#fs_fs_appendfilesync_file_data_options)
3. write `ourLog("Hello, World!")` at the bottom of the file
4. run `node logger.js` from the terminal
5. does a `log.txt` file appear? whats inside it?

## Node Modules - Exporting

If we want our code to be useable by other modules, we need to *export* parts of it using the `module.exports` object. What you assign to `module.exports` in one module is what will be returned when `require`d from another module.

Create a `main.js` and write

```js
var logger = require("./logger")
```

Now in `logger.js` try exporting the `ourLog` function in the following two ways. For each way, try and use it from `main.js` and observe the difference, remembering that whatever you assign to `module.exports` in one module is the exact value that will be returned when `require`d from another module;

```js
module.exports = ourLog;
```

```js
module.exports = { log: ourLog; }
```

#### Things to Note

- Where node looks for a `require`d module varries depending on the string given to `require`:
  - If it is the name of a core module (`http`, `fs`, `os` etc) the core module is loaded
  - If it is the name of a npm module, npm will check the project's node modules and then global node modules
  - If the string is a relative path, node will load that file. (If it is a relative path to a directory, node will look for an `index.js` in that directory)
- A `module` isn't actually a global object, but rather, it is local to each module (i.e. the file it is being defined in). However, we can use the `exports` property on modules (`module.exports`) to specifically declare what from the module we want to be made available to other modules/files through the use of `require()`
  - [Eloquent JavaScript Ch 10: Modules](http://eloquentjavascript.net/10_modules.html) is a great breakdown of the usefulness of modules and gives a simplified example of what exactly Node is doing when we require a file
- The module's source file is only executed the first time that file is required.

## npm - "Node Package Manager" ( 20 mins)

Node uses a package management system to distribute and manage open-source modules.

We use the _Node Package Manager_ by running its commands, `npm`.

 - Documentation [HERE](https://docs.npmjs.com/getting-started/what-is-npm).

### Basics of npm

Npm is two things:

- an *online repository* of node projects/modules
- a *command line utility* that aids in installation, distribution, and version and dependency managment of modules

#### Installing and Using a Package

Move into the `practice` folder and install the `faker` package

```bash
cd practice
npm install faker
```
(ignore the warnings for now)

Now, start a REPL session and load `faker`

```js
const faker = require("faker")

faker.name.findName()
```

#### `node_modules`

Where does is `faker` code stored? Try this in the terminal

```bash
ls node_modules
```

`npm` installs packages into a folder called `node_modules`. This is where node loads packages from as well. Each project has its own `node_modules` directory. The modules the project uses are the projects **dependencies**. It is also possible to install dependencies globally by using a special option but we usually do this to install tools rather than project dependencies.

#### `package.json`:

`npm` uses a file named `package.json` to keep track of dependencies. When we installed `faker` earlier, npm complained that `package.json` didn't exist. We can use the command `npm init` to create a `package.json` file. At a minimum, it must have name and version properties defined within it:

```js
{
  "name": "node_practice_app",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "moment": "^2.18.1"
  }
}
```

`npm` will guide you through this by running `npm init`. Try that now.

> Note on semantic Versioning: It follows a pattern of MAJOR.MINOR.PATCH.
Read more about it [HERE](http://semver.org/)

[HERE](https://docs.npmjs.com/cli/help) are more CLI commands for npm.
We can bring external packages to our application by running `npm install [options][package name]`. There are multiple optional *flags* you can add to the installation.

A few important ones are:
- `--save` will add the package as a dependency to `package.json` for distribution. This way, when other people clone it, and run `npm install`, the package will be automatically installed in the working directory under node-modules (that also magically appeared when you run `npm install`) :thumbsup: (NOTE: as of a few months ago `npm` will automatically add new installations to your `package.json` so the `--save` option isn't necessary)
- `--save-dev` will add the package under devDependencies to your `package.json`. Use this option when you want to download a package for developers, such as grunt, gulp (task runners). Thus, when you are distributing your code to production, these dependencies will **not** be available.
- `--global` will add the package to your global `node_modules` directory but not your current project's `package.json`. We really only want to use this for node tools (like `npm`) but not project dependencies.

### .gitignore (5 min)
Since `package.json` is your way of documenting dependencies. There is no need to push the `node_modules` folder to gitHub (Also, it can get pretty large).
This is when `.gitignore` file comes in. The word speaks for itself: all folders and files included in it, will be safe and sound on your local machine and not pushed to gitHub.

> Demo

### Exploration (10 min)

`npm` distributes modules, but also whole terminal programs written in JavaScript.

Take this time to explore interesting packages [HERE](https://github.com/sindresorhus/awesome-nodejs)

Check out [Chalk](https://github.com/chalk/chalk),
[lodash](https://lodash.com/), [empty-trash](https://github.com/sindresorhus/empty-trash).

Then we will discuss your findings!

You'll use this in a handful of lessons in the coming week. For now, practice making a quick module of your own!

### LAB 20 (min)

Go to `practice/carz` folder within this directory and you will find all the instructions there :thumbsup:

**DONE?** :tada:

Create a separate file within `practice` directory, read the documentation for  [MomentJS](http://momentjs.com/docs/), and **import** it into your application. Use these lecture notes as a reference. Play around with it.

1. What does it do?
2. What are the benefits?
3. Can you figure out what day of the week July 20 3928 will be?
4. Can you write that answer out to a text file?

## Conclusion (10 mins)

<!-- Note: Review the solution to the independent practice -->

- What are some of the important distinguishing features of Node?
- Demonstrate how to run JS on your computer, both interactively and in a file
- Demonstrate how `module.exports` & `require` work
