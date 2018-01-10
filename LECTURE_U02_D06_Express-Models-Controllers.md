---
title: Express Models & Controllers
type: lesson
creator:
    name: Vince Abruzzo
    modified by: J Silverstein
    city: NY
competencies: Express, Backend
---


# Express Controllers & MVC

### Learning Objectives

- Revisit the MVC pattern
- Review setting up a database, running a migration, and seeding it
- Explain the concept of a model, implement a model
- Explain how to refactor our routes to utilize the MVC pattern
- Explain the concept of a controller, implement a controller

# Before we get started...

I saw a LOT of homework submissions that said something like "I can do the express stuff if I follow the lecture notes, but I need more time before I can do it off the top of my head." It's **totally fine** to look at the lecture notes to build your express stuff!! I look at my own lecture notes every single time I go to build an app. (That's why they're so detailed, I forget things.)

And one of my issue tickets from when I was learning express. Sound familiar?

![issue ticket](./assets/j-issueticket.png)

# MVC Revisited

- The Model View Controller (MVC) programming design pattern is a popular coding pattern that favors splitting up an application into multiple logical pieces.
- This pattern is employed to bring testable, modular components to an application's architecture that are easy to maintain.

### Views

- Views contain the HTML code as well as basic logic to display dynamic information inside of the markup.
- We'll talk about how to manage views tomorrow!

### Controllers

- Controllers are the brains of the MVC application.
- When they are called by the router they are responsible for interacting with models and eventually rendering the views.

### Models

- The word "model" here is a two part meaning.
- First of all, it is code that is meant to interact directly with some data persistence layer such as a database.
- Secondly, models are object representations of the data in the database.
- For our application we are using PostgreSQL as our data persistence layer and [pg-promise](https://github.com/vitaly-t/pg-promise) as our database "client" that will interface with the actual database.

## What are we about to do?
In the `tc-flashcards-begin` directory you'll find a clone of where we left off yesterday. We are going to refactor that app into an MVC pattern and add the necessary part so that we can use an actual database. The complete refactored app is in the `tc-flashcards-final`. You may use that as a reference during the labs, but, **no copy and pasting**. Remember, it's science.

# Part 1: Let's set up our database.

(_Don't_ follow along; you'll get a chance to catch up during lab time.)

1. In the db directory, I added a `config.js` file, a migrations directory that contains a migration file, and a seeds directory that contains a few seed files. A migration typically sets up some tables, and seed files add some mock, test, or starter data to our database.

2. Create the database in psql:
```sql
CREATE DATABASE tc_flashcards_dev;
```

3. Run the migration from the terminal to create tables, this command will work from inside the migrations directory:
`psql -d tc_flashcards_dev -f migration-101620171530.sql`
    - Notice how the migration is titled with the date & time it was created. This is convention. Generally once a migration file has been run it shouldn't be modified. This is less strict in Express World than it is in Rails World as we'll learn later, but for now, if you realize that you've made a mistake in your migrations, it's better to make a new migration file that updates your columns or whatever than it is to edit the existing migration.

4. Run the seed files. This command will work from within the seeds directory:
`psql -d tc_flashcards_dev -f seed.sql`

5. Set up the config file:
```javascript
const options = {
  query: (e) => {
    console.log(e.query);
  }
};

const pgp = require('pg-promise')(options);

function setDatabase() {
  if (process.env.NODE_ENV === 'development' || !process.env.NODE_ENV) {
    return pgp({
      database: 'tc_flashcards_dev',
      port: 5432,
      host: 'localhost',
    });
  } else if (process.env.NODE_ENV === 'production') {
    return pgp(process.env.DATABASE_URL);
  }
}

const db = setDatabase();

module.exports = db;
```

We are using a node module called pg-promise. pg-promise is the javascript module that will talk to our PostgreSQL database for us. This config file includes information about our database(s), passes that information to an instance of pg-promise that we create, and then assigns and exports that instance of pg-promise to a variable called `db`.

## 🚀 Lab 1

Follow along and complete the steps from part 1. If you run into any errors or have any questions, please take note. We will address errors and questions before moving on.

- cd into `tc_flashcards-begin`
- run `npm install` to install the dependencies already in `package.json`
- run `npm install --save pg-promise` to install pg-promise
- create the database `tc_flashcards_dev`
- run the migration file and then the seed file as described above
- within the `db` directory create `config.js`
- within `config.js` _type out_ the config file above


# Part 2: Get information about all flashcards from the flashcards database

### The model

The model that we create in our app will be responsible for doing the actual interfacing with the database. In this case, the model will be where pg-promise actually inserts, updates, and deletes from the database. This is where the CRUD happens.

1. First create a `models` directory in the root of the project. Inside that directory create a `Flashcard.js` file.

2. At the top of that file, insert:

```js
const db = require('../db/config');

const Flashcard = {};
```

Here, we first import the `pg-promise` instance that we created in step one. Then, we declare a `Flashcard` object. Eventually, we'll export this, after we add some methods to it.

3. The method we'll add to get all the information from the flashcards table  will be called `findAll`. It looks like this:

```js
Flashcard.findAll = () => {
  return db.query('SELECT * FROM flashcards');
}
```

We named that method `findAll`. We assign it to an arrow function. That arrow function is going to return a promise, since it takes time to go and get stuff from the database. The syntax for this database query is simply `db.query(SQL query)`. Recall that `db` is just our instance of `pg-promise`. `query` is one of the methods we can use with `pg-promise`. It just executes an SQL query.

4. Of course, don't forget to export the `Flashcard` object.

```js
module.exports = Flashcard;
```

### The controller

Now that we have a function that gets all the information from our database, we need to call it somewhere. We're going to call it in a _controller_. As you recall, the controller is what handles requests and responses. It creates the functionality of our app.

Like the model, our controller is going to be an object with methods. These methods are the brains of the app. The routes send matching requests to the controllers which then ask the database to do things. After the database is done, they then send our response. 

1. Create a new directory in the root of the app called `controllers`. Then, make a file in that directory called `flashcards-controller.js`.

2. Let's import the flashcards model and declare an empty flashcards controller object that we'll eventually export.

```js
const Flashcard = require('../models/Flashcard');

const flashcardsController = {};
```

3. Now we're going to make the controller for the flashcards index. It will be called when someone `GET`s the `/flashcards` route, and will ask the model to get all the flashcards and send them back as JSON data.

```js
flashcardsController.index = (req, res) => {
  Flashcard.findAll()
    .then(flashcards => {
      res.json({
        message: 'ok',
        data: {
          flashcards: flashcards,
        },
      });
    }).catch(err => {
      console.log(err);
      res.status(500).json(err);
    });
}
```

Note that `.then` is followed by `.catch`. This is how we handle errors in promises. It could be anything from a database error to a server error. For example, if the database is down and you try to query it, you'll get an error and `res.json()` won't get called. Instead we just send an error.

4. And, finally, export the controller.

```js
module.exports = flashcardsController;
```

### The route

Finally, we need to modify our route so that it uses the controller.

In `routes/flashcard-routes.js`:

1. Import the `flashcardsController` from its file.

```js
const flashcardsController = require('../controllers/flashcards-controller');
```

2. If we look back at the index controller, we can see that it currently performs the same function as the `/flashcards` index route. Therefore, we can pass the controller's index method into the route as the callback function instead of defining it inline.

```js
flashcardRouter.get('/', flashcardsController.index);
```

Now, when we go to `localhost:3000/flashcards`, we get information about all the flashcards.

## 🚀 Lab 2

Follow along and complete the steps from part 2. If you run into any errors or have any questions, please take note. We will address errors and questions before moving on.

- Create a directory `models`. Within that directory create a file `Flashcard.js`.
- Import `db/config.js`.
- Initialize an empty object called `Flashcard`.
- Add a method to `Flashcard`, `findAll`, that makes use of the `pg-promise` `query()` method.
- Export the object `Flashcard` from the model file.
- Create a directory `controllers`. Within that directory create a file `flashcards-controller.js`.
- Import `models/Flashcard.js`.
- Create an empty object called `flashcardsController`. 
- Add a method `index` to the `flashcardsController` object.
- Export the `flashcardsController` object.
- In `routes/flashcard-routes.js`, import `controllers/flashcards-controller` and modify the `.get('/', ...` route so it uses the `flashcardsController.index` method.

# Part 3: Accessing individual records in the database

So! By the time our controller and model is done, we're going to have five separate routes:

- GET `/flashcards` -- to get all flashcards
- GET `/flashcards/:id` -- to get an individual Flashcard
- POST `/flashcards` -- to add a new Flashcard
- PUT `/flashcards/:id` -- to update information about a Flashcard
- DELETE `/flashcards/:id` -- to delete a Flashcard from the database

We've already done the first one. The `PUT` and `POST` routes require us to send information to the server -- we'll tackle those last. The individual flashcards `GET` route and the `DELETE` route just require us to send the correct parameters in the URL, so we'll add those next.

### The model

We're going to add two methods to our `Flashcard` object: `findById` and `destroy`.

In `models/Flashcard.js`:

```js
Flashcard.findById = (id) => {
  return db.oneOrNone(`
    SELECT * FROM flashcards
    WHERE id = $1
  `, [id]);
}
```

We're making a new method on our `Flashcard` object called `findById` and setting it equal to an arrow function. The arrow function takes a parameter `id`, which refers to the id in the database of the flashcard we want to find. 

Instead of using the `query` method, we're using the `oneOrNone()` method. Since we're trying to find a single flashcard, this method will handle that whether or not there is one. The SQL query is only slightly different from what we've seen.

Instead of putting the `id` variable right in the query, we use the `$1` syntax, and then pass a second argument to the method. The second argument is an array that contains all of the values for the items we need in our SQL query. This is a security measure that prevents something called SQL _injection_. 

Here is the classic example of SQL injection:

![bobby tables](https://imgs.xkcd.com/comics/exploits_of_a_mom.png)

When the query goes to the database, `$1` will be replaced with the first thing in the array, the id.

The `destroy` method looks similar:

```js
Flashcard.destroy = (id) => {
  return db.none(`
    DELETE FROM flashcards
    WHERE id = $1
  `, [id]);
}
```

We know we don't need to have anything returned back from this query, so we're using the `none()` method from `pg-promise`.

### The controller

In `controllers/flashcards-controller.js`, let's write the methods that talk to the model methods we just made.

```js
flashcardsController.show = (req, res) => {
  Flashcard.findById(req.params.id)
    .then(flashcard => {
      res.json({
        message: 'ok',
        data: {
          flashcard: flashcard,
        },
      });
    }).catch(err => {
      console.log(err);
      res.status(500).json(err);
    });
};

flashcardsController.delete = (req, res) => {
  Flashcard.destroy(req.params.id)
    .then(() => {
      res.json({
        message: 'Flashcard deleted successfully!',
      });
    }).catch(err => {
      console.log(err);
      res.status(500).json(err);
    });
}
```

In both of these, we're passing `req.params.id` into the model's method. 

In the `show` method, we're getting a flashcard back from the database and sending it as json.

In the `delete` method, we're not getting anything back from the database -- we're just sending back a message that it worked.


### The routes

Now, we need to modify our `GET /flashcards/:id` route and create a `DELETE /flashcards/:id` route that talks to the new model methods.

In `routes/flashcard-routes.js`:

```js
flashcardRouter.get('/:id', flashcardsController.show);
flashcardRouter.delete('/:id', flashcardsController.delete);
```

Even though both of these are to the same endpoint, `/flashcards/:id`, they have different HTTP verbs, so we can use them both without any problems.

## 🚀 Lab 3

Follow along and complete the steps from part 3. If you run into any errors or have any questions, please take note. We will address errors and questions before moving on.

- Add `findById` and `destroy` methods to the `Flashcard` object in `models/Flashcard.js`
- Add `show` and `delete` methods to the `flashcardsController` object in `flashcards/flashcards-controller`
- Modify the `.get('/:id', ...` route and create a `.delete('/:id, ...` route that both use your new controller methods.

# Part 4: Adding information to the database

### The model

Now we're going to create a method called `create`. This is also an arrow function, it also returns a promise, and it inserts a new flashcard into our database.

```javascript
Flashcard.create = (flashcard) => {
  return db.one(
    `
      INSERT INTO flashcards
      (question, answer, difficulty, category)
      VALUES ($1, $2, $3, $4) 
      RETURNING *
    `,
    [flashcard.question, flashcard.answer, flashcard.difficulty, flashcard.category]
  );
};
```

 The argument that this method takes is an object with new flashcard info, which looks like this:

 ```js
 {
   question: 'question',
   answer: 'answer',
   difficulty: 1,
   category: 'category',
 }

 ```

Note that now, since we have to reference four variables in our SQL query, we have items called `$1`, `$2`, `$3`, `$4` inside the query itself. Then we pass an array that has four values. `$1` will be filled in with `flashcard.question`, and so on. Again, this is a security measure. 

### The controller

Now we'll add the controller that will be responsible for adding new flashcards:

```javascript
flashcardsController.create = (req, res) => {
  Flashcard.create({
      question: req.body.question,
      answer: req.body.answer,
      difficulty: req.body.difficulty,
      category: req.body.category,
    })
    .then(flashcard => {
      res.json({
        message: 'Flashcard added successfully!',
        data: {
          flashcard: flashcard,
        },
      });
    })
    .catch(err => {
      console.log(err);
      res.status(500).json(err);
    });
};
```

Note that it calls the `create` method that we defined in our flashcard model.

There's something new here, `req.body`. This requires a new NPM package and a new middleware, `body-parser`. Install this with the command `npm install --save body-parser`. Then, we have to tell the app to use this in `server.js`. Add these lines into `server.js`:

```js
const bodyParser = require('body-parser');
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: false}));
```

### The route

The create route will be a `POST` request to `/flashcards`. Add this to `routes/flashcard-routes.js`:

```js
flashcardRouter.post('/', flashcardsController.create);
```

Let's test this in postman. We'll get a clearer idea of what `body-parser` does.

## 🚀 Lab 4

Follow along and complete the steps from part 4. If you run into any errors or have any questions, please take note. We will address errors and questions before moving on.

- Add a `create` method to the `flashcards` object in `models/Flashcard.js`.
- Add a `create` method to the `flashcardsController` object in `controllers/flashcards-controller.js`.
- Install `body-parser` and require it in `server.js`. Add the two `app.use` lines as well.
- Add the post route to `routes/flashcard-routes.js`.

# Part 5: Putting it all together!!!

We're almost done!!! Can you believe it? The last thing we have to do is make a way to edit flashcards in the database. This will require both the use of params and the use of the request body.

### The model

Let's add a new method to our model, `update`.

```js

Flashcard.update = (flashcard, id) => {
  return db.one(`
    UPDATE flashcards SET
    question = $1,
    answer = $2,
    difficulty = $3,
    category = $4
    WHERE id = $5
    RETURNING *
  `, [flashcard.question, flashcard.answer, flashcard.difficulty, flashcard.category, id]);
}
```

This method takes two arguments, a flashcard object and an id. Then, it updates the flashcard where the id matches.

### The controller

Let's add an `update` method to our controller. Notice that we're still using `req.body`. We're also using `req.params.id`.

```javascript
flashcardsController.update = (req, res) => {
  Flashcard.update({
    question: req.body.question,
    answer: req.body.answer,
    difficulty: req.body.difficulty,
    category: req.body.category,
  }, req.params.id).then(flashcard => {
    res.json({
      message: 'Flashcard updated successfully!',
      data: {
        flashcard: flashcard,
      },
    });
  }).catch(err => {
    console.log(err);
    res.status(500).json(err);
  });
};
```

### The route

Last piece of the puzzle!!!

In `routes/flashcard-routes.js`:

```js
flashcardRouter.put('/:id', flashcardsController.update);
```

Test it out in Postman, and we're done.

## 🚀 Lab 5

Follow along with the steps in part 5. Then, you'll be all done!

- Add an `update` method to the `Flashcard` object in the model
- Add an `update` method to the `flashcardsController` object in the controller
- Add a `.put('/:id', ...` route to the routes

# WE MADE IT!!! 🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉🎉

(I'm just as pumped as yall are.)

### To recap:

- We use the module `pg-promise` to connect our postgres database with our express app.
- `pg-promise`, as the name suggests, uses JavaScript promises to handle getting data out of the database. We access the result of these promises using `.then`, and handle errors using `.catch`.
- Instead of sending back the data directly in our routes, we manage the connection between our models and our routes using controllers.
- Express is very flexible; there are a billion ways to do things.
- Today was long and hard. You're all superheroes.

