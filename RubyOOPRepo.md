# Intro to Object-Oriented Programming in Ruby

## Learning Objectives

- Explain the principles of Object Oriented Programming
- Describe the relationship between a class and an instance
- Define a Ruby Class and instantiate it
- Explain the relationship between `.new()` and `def initialize()`
- Distinguish local, instance, and class variables
- Examine interactions with objects through methods
- Understand the difference between `attr_accessor`, `attr_reader`, `attr_writer`, and know when to use each one.  

## Framing: What is OOP? (15 minutes / 10:15)

The earliest computer programs were written in an **imperative** style.
The programs were series of instructions that changed a program's **state**.
Data was loaded, the program ran and manipulated the data, and some output was produced.
The first JavaScript and Ruby programs we wrote were **imperative**.  

Large applications written in imperative style can become confusing.
To consider the current state of the application, a developer needs to consider all of code that had been run so far.
This process becomes increasingly difficult as the application grows.

[Good article explaining the difference between imperative and declarative programming](https://tylermcginnis.com/imperative-vs-declarative-programming/)

Object oriented programming attempts to solve this by organizing applications into discrete units called **objects**.
An object keeps any state or data relevant to itself internally.
We almost always have many **instances** of a type of object.
We use **methods** to initialize, modify, or act on an objects' internal state.
Because these methods will be the same across all instances of a type of object, we define a single **class** with all of this behavior described in one place.
We then use that class to instantiate as many instances (objects) of the class as we need.

Object oriented programming gives use three major benefits:
- **Encapsulation**: The complexity of our objects is hidden and a user can interact with a well defined set of methods. We can redefine internal behavior without changing the external - a huge benefit for refactoring.
- **Modularity**: Because the interface (set of methods) for an object is well defined, it is easy to exchange one object for one another and reorganize objects without breaking them.
- **Inheritance**: As we saw in JavaScript and will soon explore in Ruby, Object Oriented design lets certain classes **inherit** from, or borrow the behavior of another, more general class. The inheriting class can add and overwrite inherited methods.

You can think of an object as a little machine: it has displays you can read and buttons you can push.
When you write an object-oriented application, you are modeling the entire program as the interactions of these machines.

Parts of this class is adapted from Dr. Ana Bell's [Introduction to Programming in Python class at MIT](https://youtu.be/-DP1i2ZU9gk).

## Ruby Classes (20 minutes / 10:35)

The distinction between **classes** and **instances** may seem foreign and technical but, in reality, it is very similar to how we think about the real world.

I can say "imagine a car" and we have no problem doing so.

I can also point at something, ask "is that a car?" and get near universally consistent answers.

We can even look at a blueprint for the car and it will have a description, a basic drawing, and technical specifics about the car it represents.

![Car Blueprint](img/blueprint.png)

The *general* idea of the "car" is the **class**. Each of us has a mental model of what a car is: it has four wheels, runs on gas, has a steering wheel that allows us to drive it, etc.

The tangible metal machine is the **instance**, and we can have lots of those cars that follow the same blueprint.

![Cars](img/cars.jpg)

There are two different aspects of our data that we want our classes to represent. The first are the data properties, or **attributes** that make up our car. Some examples of these are its length, color, height, number of doors, its engine type etc. There will also be actions associated with it, which we will call **methods**. Some examples of these actions are driving the car, shutting its doors, and honking its horn.

It turns out that this isn't the first time we've seen objects in Ruby -- every data type in Ruby is a class. If we create an array in our `pry` console, we can see that there are methods we can run on it, and we can access its data at each index.

If you are interested in seeing more about this, you can access the `.class` method on any object in Ruby.

Let's now transition into writing our own classes.

Let's define a `User` class. We'll be using `binding.pry` to test our code.

> Aside: pry is a ruby gem that allows us to work with ruby code in an IRB (interactive ruby shell). It's a REPL like the one javascript console in the browser.

```bash
$ touch app.rb
$ gem install pry # run this if you haven't installed pry yet
```

```ruby
require "pry"

class User

  def set_name(some_string)
    @name = some_string
  end

  def get_name
    return @name
  end

  def greet
    puts "Hi! My name is #{@name}!"
  end

end

me = User.new
me.set_name("Perry")
me.greet # prints: Hi! My name is Perry!

binding.pry

puts "end of file"
```

In JavaScript, we could write this class:

```js
class User {
  setName (name) {
    this.name = name
  }
  getName () {
    return this.name
  }
  greet () {
    console.log(`Hi! My name is ${this.name}`)
  }
}

const me = new User()
me.setName('Perry')
me.greet() // prints: Hi! My name is Perry!
```

These two snippets are very similar.
What differs between the two?

Notice the `@` that starts every **instance variable**.
Instance variables are like properties of JS objects - the objects' internal data.
The main difference is that in Ruby, *instance variables can only be accessed/changed by methods* of that object.
More on that when we discuss getters and setters below.

By instantiating multiple `User` objects, we can see that all of the methods defined for the class can be called on any instance of `User`.

```ruby
alice = User.new
alice.set_name("Alice")
puts alice.get_name

madhatter = User.new
madhatter.set_name("Mad Hatter")
puts madhatter.get_name

alice.greet
madhatter.greet
```

#### Some Questions

Is `User` a(n)...
- class?
- instance?

Is `alice` a(n)...
- class?
- instance?

`User.greet` throws an error. `alice.greet` works fine. So we can deduce that the `greet` method can only be called on...
- instances of the `User` class?
- the `User` class itself?

Thus, would it make sense to call `greet` a(n)...
- "instance method"?
- "class method"?

`User.new` works fine. `alice.new` throws an error. So we can deduce that the `new` method can only be called on...
- instances of the `User` class?
- the `User` class itself?

Thus, it would be make sense to call `new` a(n)...
- "instance method"?
- "class method"?

<details>

  <summary>
    `class User` works fine. `class user` throws an error. What's a rule we can deduce about classes from this?
  </summary>

  > Class names must begin with a capital letter. This is not optional.

</details>


<details>
  <summary>
    `class UserName` works fine. `class User Name` throws an error. What's a rule we can deduce about classes from this?
  </summary>

  > Class names cannot contain spaces.

</details>

## Initializing Users (15 minutes / 10:50)

<details>
  <summary><strong>What was the purpose of a constructor function in Javascript classes?</strong></summary>

  > When an instance of a class is created (the class is instantiated) the constructor function is run to do set up (or initialization) of any properties we want a class instance to have.

</details>

Ruby classes have a method analogous to `constructor` in JS: the `initialize` method

```ruby
require 'pry'

class User

  def initialize
    puts "I'm a new User"
  end

  def set_name(some_string)
    @name = some_string
  end

  def get_name
    return @name
  end

  def greet
    puts "Hi! My name is #{@name}!"
  end

end
```

```ruby
alice = User.new

madhatter = User.new


puts alice
puts madhatter
```


<details>
  <summary>What can we conclude about the relationship of `def initialize` and `.new`? (Hint: it serves the same purpose as Javascript's constructor function)</summary>
- When the class method `.new` is called, a new object is instantiated and the instance method `initialize` is called with the arguments given to `.new`
</details>


<details>
  <summary>How is this different from other User instance methods we've seen?</summary>

   `initialize` can only be called by the `.new` class method (i.e. it only runs once - when the object is initially created).

</details>

---
`initialize` methods are often used to set values for instance variables:

```ruby
class User

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

end
```

```ruby
# pry
harry = User.new("Harry", "Potter")
# => #<User:0x007faf3903f670 @firstname="Harry", @lastname="Potter">
harry.full_name
# => "Harry Potter"
```

### Ruby Variables (10 minutes / 11:00)

In our exploration of Ruby so far, you may have notice that scope works differently than in JavaScript.
Specifically, you may have seen that in Ruby, normal variables are available only inside the method in which they were created.

This is because Ruby is intended to always be written in an object oriented style.
Instead of having access to data defined previously in the code (called lexical scope), Ruby gives you access to internal data of the object a method is called on.

- **Global Variables**: start with `$` (`$my_global_var`) and is available anywhere in the Ruby app. Not very common.
- **Local Variables**: starts with any letter (`my_local_var`) and is available only in the method in which it is defined. Very common.
- **Instance Variables**: starts with `@` (`@my_instance_var`) and is available to any method called on the instance. Very common.
- **Class Variables**: starts with `@@` (`@@my_class_var`) and is available to all instances of a class. Not very common (we'll look at these in more detail later on).

## Break (10 minutes / 11:10)

### Getting and Setting Instance Variables (20 minutes / 11:30)

Let's update our JavaScript user class with a constructor to match our initialize method:

```js
class User {
  constructor (firstName, lastName) {
    this.firstName = firstName
    this.lastName = lastName
  }
  fullName () {
    return this.firstName + ' ' + this.lastName
  }
}

let harry = new User('Harry', 'Potter')
```

With our JS instance, we can call `harry.fullName()` and get back the concatenated full name.

We can also access the properties on `harry` using dot notation: `harry.firstName` and `harry.lastName`

This is a major criticism of JavaScript as it is seen to violate encapsulation.

In Ruby, we can't simply type `harry.firstname`. Ruby will try to find a method `firstname` and will throw an error when one isn't found. Trying to **set** Harry's first name throws a similar error `harry.firstname = "Harry"`

The only things available **outside** an instance are its methods. `@firstname` is a property, not a method.
If we want to get at the instance variables, we need methods to do that.
In Object Oriented programming there is an extremely common pattern of defining [**getter** and **setter** methods](https://en.wikipedia.org/wiki/Mutator_method) that allow internal variables to be read/manipulated.

Why might a method be preferable to an exposed variable?

```ruby
class User

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  def get_firstname
    return @firstname
  end

  def set_firstname(firstname)
    @firstname = firstname
  end

end
```

```ruby
# pry
harry = User.new("Harry", "Potter")
# => #<User:0x007faf3903f670 @firstname="Harry", @lastname="Potter">
puts harry.get_firstname
# "Harry"
harry.set_firstname("Ginny")
puts harry.get_firstname
# "Ginny"
```
Though Javascript perhaps made it too easy to access and change our properties, the dot syntax (`object.property`) was pretty convenient.

By defining a getter method with the same name as the property being retrieved, we can make our initial attempt at reading the variable (`harry.firstname`) work:

```ruby
class User

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  def firstname
    return @firstname
  end

  def set_firstname(firstname)
    @firstname = firstname
  end

end
```

```ruby
harry = User.new("Harry", "Potter")
puts harry.firstname
# => "Harry"
```

A nice bit of Ruby syntactical sugar lets us also write setter methods that look like assignment.

Recall the error we saw after trying `harry.firstname = 'Ginny'`. It was `undefined method 'firstname='`

Ruby will let us define a method ending in an `=` and when we call it, have a space between the last letter in the method name and the `=`.

The important thing here is to note that while this looks like an assignment, it is not, it is a method call.

```ruby
class User

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  def firstname
    return @firstname
  end

  def firstname=(firstname)
    @firstname = firstname
  end

end

harry = User.new("Harry", "Potter")
harry.firstname = "Ginny" # same as harry.firstname=("Ginny") or harry.firstname = ("Ginny")
puts harry.firstname
# => Ginny
```

### attr_accessor

This is such a common pattern that it is built into Ruby in **accessors**.

The three accessors are `attr_reader`, `attr_writer`, and `attr_accessor`.

They take symbols as arguments and will create (respectively) **getter**, **setter**, or both methods for instance variables.

The following snippets replace the manually defined getter and setter with the accessors

### attr_reader
```ruby
class User
  attr_reader :firstname

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  # def firstname
  #   return @firstname
  # end

  def firstname=(firstname)
    @firstname = firstname
  end

end

harry = User.new("Harry", "Potter")
harry.firstname = "Ginny" # Using the manually defined method
puts harry.firstname # Using the attr_reader defined method
# => Ginny
```

### attr_writer
```ruby
class User
  attr_reader :firstname
  attr_writer :firstname

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  # def firstname
  #   return @firstname
  # end

  # def firstname=(firstname)
  #   @firstname = firstname
  # end

end

harry = User.new("Harry", "Potter")
harry.firstname = "Ginny" # Using the attr_writer defined method
puts harry.firstname # Using the attr_reader defined method
# => Ginny
```

### attr_accessor
```ruby
class User
  attr_accessor :firstname
  # attr_reader :firstname
  # attr_writer :firstname

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  # def firstname
  #   return @firstname
  # end

  # def firstname=(firstname)
  #   @firstname = firstname
  # end

end

harry = User.new("Harry", "Potter")
harry.firstname = "Ginny" # Using the attr_accessor defined method
puts harry.firstname # Using the attr_accessor defined method
# => Ginny
```

Note: an accessor can be used for multiple variables at once:
```ruby
class User
  attr_accessor :firstname, :lastname
  # attr_reader :firstname
  # attr_writer :firstname

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  # def firstname
  #   return @firstname
  # end

  # def firstname=(firstname)
  #   @firstname = firstname
  # end

end

harry = User.new("Harry", "Potter")
harry.firstname = "Ginny"
harry.lastname = "Weasley"
puts harry.firstname
puts harry.lastname
# => Ginny
```

<details>
  <summary>We now can directly access properties on the User instance, so we can deduce that `attr_accessor` is a shortcut that does what?</summary>

  > It creates getter and setter methods for the `firstname` instance variable.

</details>

`attr_reader` makes an attribute readable, `attr_writer` makes an attribute writeable. `attr_accessor` makes an attribute both readable **AND** writeable.


## You Do: Monkies! (20 minutes / 11:50)

For the next exercise, clone down the repo linked below:
https://git.generalassemb.ly/ga-wdi-exercises/oop_monkey


## Break (10 minutes / 12:00)

-------

## Class Attributes / Variables (5 minutes / 12:05)

Let's come up with a way of keeping track of how many users have been created total...

```ruby
class User
  attr_accessor :firstname, :lastname
  @@all = 0

  def count
    return @@all
  end

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
    @@all += 1
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end
end
```

```ruby
harry = User.new("Harry", "Potter")
harry.count
# => 1
ron = User.new("Ron", "Weasley")
harry.count
# => 2
ron.count
# => 2
draco = User.new("Draco", "Malfoy")
harry.count
# => 3
ron.count
# => 3
draco.count
# => 3
```

But there's something weird going on here: note that we aren't counting the number of Rons, Harrys or Dracos. Think about what `.count` might be returning. More on this in a moment!

A variable name beginning with `@@` is a **class variable**. Every instance of a class has the same value for this variable. It cannot be accessed with `attr_accessor`. You have to actually create a method to access it.

### Class Attributes and Methods Together (10 minutes / 12:15)

A method name beginning with the class name is a **class method**. It is attached to the class itself, rather than to instances. There are also methods you call on `User` itself. So far we've only seen `.new`. It would make more sense if, in order to retrieve the total number of users, we ran `User.count` instead of `harry.count`...

```ruby
class User
  attr_accessor :firstname, :lastname
  @@all = 0

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
    @@all += 1
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  # You could also define this as `def self.count`, where self represents the class
  def User.count
    return @@all
  end

end
```

```ruby
ginny = User.new("Ginny", "Weasley")
ginny.count
# => Error!
User.count
# => 1
```

## Self (10 minutes / 12:25)

`self` is a special variable that contains the current instance of an object (like `this` in Javascript). It's how the object refers to it*self*.

`self` has another context as well: `def self.all` Here, `self` refers to `class User`. What does this mean? It means that the method `.all` is called on the class `User`, much like `.new`, and is therefore a class method.

```ruby
class User
  attr_accessor :firstname, :lastname
  @@all = []

  def initialize(firstname, lastname)
    @firstname = firstname
    @lastname = lastname
    # here, `self` refers to the current instance
    puts "Creating #{self.firstname}"
    @@all.push(self)
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  # Can also be written as `def User.all`
  # here, `self` refers to the class
  def self.all
    return @@all
  end

end
```

```ruby
draco = User.new("Draco", "Malfoy")
# "Creating Draco"
luna = User.new("Luna", "Lovegood")
# "Creating Luna"
bellatrix = User.new("Bellatrix", "LeStrange")
# "Creating Bellatrix"
User.all
# => [#<User @firstname="Draco">, #<User @firstname="Luna">, #<User @firstname="Bellatrix">]
```

### When Might You Use Class Variables?

While the use cases for class variables are few and far between, they do exist. One such use case would be when you want to store application-level configuration data such as the application's name, database, version, and other settings. For example:
```rb
class ApplicationConfiguration
  @@configuration = {}

  def self.set(property_name, value)
    @@configuration[property_name] = value
  end

  def self.get(property_name)
    @@configuration[property_name]
  end
end

ApplicationConfiguration.set("name", "Demo Application")
ApplicationConfiguration.set("version", "0.1")

ApplicationConfiguration.get("version")
# => "0.1"
```
> Example from [Ruby Monk](https://rubymonk.com/learning/books/4-ruby-primer-ascent/chapters/45-more-classes/lessons/113-class-variables)


## Review Questions (5 minutes / 12:30)

- What is the relationship between a class and an instance?
- What is special about the method `def initialize`?
- Explain the difference between local, instance, and class variables
- What is the difference between `attr_accessor`, `attr_reader`, and `attr_writer`?

## You Do: Orange Tree (Rest of Class)

> From Chris Pine's "Learn to Program - Second Edition": p 112, section 13.6

Make an OrangeTree class that has...

- a `height` method that returns its height in feet
  - it's initial value should be determined by some input
  - hint: you don't necessarily have to define the method
- a `one_year_passes` method that, when called, ages the tree one year. Start the age at `0`.

> Test your code.

- Each year the tree grows taller by one foot
- After 50 years the tree should "die" (its height goes to 0)

> Test your code.

- After the first 5 years, the tree bears 20 oranges
- You should be able to `count_the_oranges`, which returns the number of oranges on the tree

> Test your code.

- You should be able to `pick_an_orange`, which reduces the number of oranges by 1
- Ensure that your tree cannot have negative oranges
- Ensure that after each year your tree has 20 total oranges again

> Test your code.

- The number of oranges the tree bears each year is equal to 20 plus the age of the tree

#### Bonus

Create an `OrangeTreeOrchard` class that manages multiple `OrangeTrees`. It can...

- Age all the trees by one year
- Pick and count all the fruit
- Calculate average height and fruit of all orange trees

## Glossary

* **Class**: a blueprint for objects
* **Instance**: an object that is created using a class
* **Instance Variable**: a property that is particular to an instance
* **Class Variable:** a property that is accessible by all instances of a class
* **Instance Method**: a method that can be called by an instance of a class (e.g., `sample_user.reset_password`)
* **Class Method**: a method that can be called by a class (e.g., `User.list_user`)
* **`initialize`**: a class method that, when triggered, creates an instance and assigns initial properties
* **`.new`**: a class method that, when called, triggers its `initialize` method
* **`attr_accessor`**: a setting that allows you to directly "get" or "set" an instance variable

------

## Bonus: Public and Private Methods

### You Do

- Draw a picture of a machine, real or imaginary, that has inputs (buttons, switches, keypads...) and displays (dials, lights, screens...). Label what they do.
- Most machines have internal gauges or memories that help it make decisions: temperature monitors, voltage monitors, hard disks, and so on. These are visible only inside the machine: whoever's using the machine can't see them. Draw two of these on your machine and label them.

By default all instance and class methods are *public*, except for `def initialize` which is *private*. This means they're visible to other objects. An analogy: they're functions that have their own buttons on the outside of the machine, like a car's turn signal.

There may be methods that all other objects don't need to know about.

```ruby
class User
  attr_accessor :firstname, :lastname
  @@all = []

  def initialize(firstname, lastname, password)
    @firstname = firstname
    @lastname = lastname
    @password = encrypt(password)
    @@all.push(self)
  end

  def full_name
    return "#{@firstname.capitalize} #{@lastname.capitalize}"
  end

  def User.all
    return @@all
  end

  private
  def encrypt(input)
    return input.reverse
  end

end
```

```ruby
harry = User.new("Harry", "Potter", "Expecto Patronum")
# #<User @firstname="Harry" @password="munortaP otcepxE">
harry.encrypt("Expecto Patronum")
# Error! Private method `encrypt`
```

Putting `private` in front of methods means they can be used inside the object, but are not available outside it. An analogy: they're functions that do not have their own buttons on the outside of the machine, like a car's air filter.

`private` is useful mostly for keeping things organized. Consider jQuery: It's already cluttered enough, with all these methods like `.fadeOut` and `.css`. It has lots of other methods hidden inside it that we don't really need to know about.

## Bonus: Modules

Classes are great for packaging up related methods: all my User-related methods are in one place.

But let's say my app involves translating English into other languages: I want a "translate-to-French" method, and one for Spanish, German, and so on.

Putting those into a class doesn't really make semantic sense. A class should be a blueprint for an object. Translator methods don't really "belong" to a specific object: I may want to use them with my Users, or with blog posts, or with product descriptions.

A **module** is a lot like a class. The biggest difference is semantic: **Modules are just bundles of related methods. They're not a blueprint for an object.** You may have heard about **mixins**; in Ruby, **modules** are how **mixins** are implemented.

> The module we've explored most recently is **enumerables**. Out-of-the-box, Ruby comes with a big old `Enumerable` module that has lots of handy methods inside it, like `each`.

### Try out modules

Copy and paste this snippet into your REPL:

```rb
module TranslatorMethods
  def frenchify(input)
    input + " omelette du fromage"
  end

  def spanishify(input)
    input + " donde esta la biblioteca"
  end

  def germanify(input)
    input + " schadenfreude kindergarten"
  end
end

class User

  include TranslatorMethods
  attr_accessor :nationality

  def initialize(nationality)
    @nationality = nationality.downcase
  end

  def greet
    standard = "Hello"
    case @nationality
    when "french"
      puts frenchify(standard)
    when "spanish"
      puts spanishify(standard)
    when "german"
      puts germanify(standard)
    else
      puts standard
    end
  end

end
```

Now, copy and paste these lines one at a time:

```rb
user = User.new("French")
user.greet
user.nationality = "German"
user.greet
```

It's just as if we had copy-and-pasted all those "translator" methods right into the `User` class.

<details>
<summary>What Javascript things have we seen that would make a good Ruby module? (That is: where have we seen big bundles of methods in Javascript?)</summary>
jQuery
</details>

<details>
<summary>Unrelated: Why does it say `@nationality = nationality.downcase`? What's the utility of the `downcase`?</summary>
Ruby is case-sensitive. Without this, if a user entered "FRENCH" or "French", it wouldn't register it as being the same as "french".
</details>

## Review: Why OOP?

#### Easy to Understand

Objects help us build programs that model how we tend to think about the world.
Instead of a bunch of variables and functions (procedural style), we can group
relevant data and functions into objects, and think about them as individual,
self-contained units. This grouping of properties (data) and methods is called
*encapsulation*.

#### Managing Complexity

This is especially important as our programs get more and more complex. We can't
keep all the code (and what it does) in our head at once. Instead, we often want
to think just a portion of the code.

Objects help us organize and think about our programs. If I'm looking at code
for a Squad object, and I see it has associated *people*, and those people can
dance when the squad dances, I don't need to think about or see all the code
related to a person dancing. I can just think at a high level "ok, when a squad
dances, all it's associated people dance". This is a form of *abstraction*... I
don't need to think about the details, just what's happening at a high-level.

#### Ensuring Consistency

One side effect of *encapsulation* (grouping data and methods into objects) is
that these objects can be in control of their data. This usually means ensuring
consistency of their data.

Consider the bank account example... I might define a bank account object
such that you can't directly change it's balance. Instead, you have to use the
`withdraw` and `deposit` methods. Those methods are the *interface* to the
account, and they can enforce rules for consistency, such as "balance can't be
less than zero".

#### Modularity

If our objects are well-designed, then they interact with each other in
well-defined ways. This allows us to refactor (rewrite) any object, and it
should not impact (cause bugs) in other areas of our programs.

## Extra Practice: Scrabble

Clone this exercise and follow the instructions in the readme.

**[Scrabble Word Scorer](https://git.generalassemb.ly/ga-wdi-exercises/scrabbler)**

## Resources

- [Variables cheat sheet](variables.md)
- Other exercises
  - [Monkeys](https://git.generalassemb.ly/ga-wdi-exercises/oop_monkey)
  - [Application Config](https://git.generalassemb.ly/ga-wdi-exercises/ruby_application_configuration)
  - [Superheroes](https://git.generalassemb.ly/ga-wdi-exercises/superheros)
- Screencasts
  - WDI8, Robin
    - [Part 1](https://www.youtube.com/watch?v=-pAFSheGDr0)
    - [Part 2](https://www.youtube.com/watch?v=ZgZCtns27pE)
    - [Part 3](https://youtu.be/npM249VzB0I)
    - [Part 4](https://youtu.be/su_XYcj_Cpk)
