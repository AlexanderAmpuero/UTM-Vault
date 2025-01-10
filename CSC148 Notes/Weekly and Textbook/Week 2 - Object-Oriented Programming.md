# Chapter 2.1 - Introduction to Object-Oriented Programming
We've seen that every object in Python has a type, and that an object's type tells us what operations can be performed and what values it can contain. As the manipulated data gets more complex, so too does our types. We create classes, which are out own custom types.

## Consider Twitter OOP
If we wanted to store the data of a tweet in Python, we would need to represent each major aspect of said tweet. It could like like this through a list.
```python 
['David', '2022-01-19', 'Hello I am so cool', 0]
```
Or we could store it as a dictionary
```python
{
 'userid': 'David',
 'content': 'Hello I am so cool',
 'created_at': '2022-01-19',
 'likes': 0
}
```
These methods of storage allow us to move the data from function to function as needed.
The issue with using these storage methods, is there is nothing stopping us from creating a "malformed" tweet. For example,
```python
[55, 'Diane', 'Older and even cooler', '2022-01-19'] # malformed list

{
 'userid': 'Jacqueline',
 'content': 'Has the most dignified cat', # malformed dictionary
 'likes': 12
}
```
The list could be ruined if we were to use the pop() function and remove the number of likes
The dictionary could be ruined if we add a new key-value pair that has nothing to do with the tweets, like a counter for how many times the leafs won. the stanley cup

Furthermore, there is no way to enforce the 280-char limit that twitter uses for its tweets

While, through a lot more code, it is possible to make these list and dictionary methods work for twitter, it would be a lot smarter to use classes. Classes allow us to control the operations that are performed on the data and ensure they are always well-formed.

## Writing A Class
### Attributes
A **class** is a block of code that defines a type of data. The built-in types of int, str, list, etc. are all defined by classes. 
Suppose we have a class called **x** and an object whose type is **x** is called in instance of class **x**, for example, **3** is an instance of the class **int**.
An instance of a class doesn't have to contain a single piece of data like in **int**, it can hold a collection of data, even data from other classes.

For example, lets see what a class for a tweet could look like.
```python
class Tweet
	"""A tweet, like in Twitter.

	=== Attributes ===
	userid: the id of the user who wrote the tweet.
	created_at: the date the tweet was written.
	content: the contents of the tweet.
	likes: the number of likes this tweet has received.
	"""
```
Now that we've created the docstring, we must create the types for each attribute below it.
```python
class Tweet
	"""A tweet, like in Twitter.

	=== Attributes ===
	userid: the id of the user who wrote the tweet.
	created_at: the date the tweet was written.
	content: the contents of the tweet.
	likes: the number of likes this tweet has received.
	"""
	# Attribute Types
	userid: str
	created_at: date
	content: str
	likes: int
```
Note that these annotations do not effect the program at all, they are just meant to help the programmers understanding

### Creating an Instance of a Class
After writing the docstring of the class body, we will create a new **type**! We are able to import the class and then create an instance of it as seen below.
```python
>>> tweet = Tweet()
```
This code creates a new Tweet object and stores a reference to it in the variable *tweet*

### Defining an Initializer
At this point, the new object tweet doesn't contain any data
```python
>>> tweet = Tweet()
>>> tweet.userid
AttributeError: 'Tweet' object has no attribute 'userid'
```
This error occurs because type annotation doesn't create the variable, so the program searches pythons memory for a variable *userid* which does not exist.
In oder to initialize instance attributes for an instance of a class, we define a special method inside the class called ____init____ or in english, an initializer. This is what it could look like. 
```python
class Tweet
	# previos content omitted for brevity

	def __init__(self, who: str, when: date, what: str) -> None:
		""" Initialize a new Tweet """
```
The *self* parameter is apart of every initializer as a first parameter that refers to the instance that has just been created and is to be initialize. By convention we call it *self*, Most code checkers with python will complain if you don't use it. 
```python
>>> from datetime import date
>>> t1 = Tweet('Giovanna', date(2022, 1, 18), 'Hello')
```
Notice how we didn't mention the initializer __*___init__*__ by name. This is because its called automatically, as the values in the parentheses are passed to it. The self parameter receives the id of the instance that is to be initialized.

Now imagine we want to assign the variable userid to something now from the class, we cannot just say userid = x and assign userid a value because it would create a new variable in the stack frame, rather, we need to put it into a new object. Fortunately for us, *self* refers to the new object, and we can go into the object by using a '.' to refer to it like so
```python
class Tweet
	# previos content omitted for brevity

	def __init__(self, who: str, when: date, what: str) -> None:
		""" Initialize a new Tweet """
		self.userid = who
		self.created_at = when
		self.content = what
		self.likes = 0
```
Now we've created four instance attributes which look like this in the memory of python.![[Screenshot 2025-01-09 at 6.45.00 PM.png]]
After we return, we can assign the id of the new object to t1 which exists in ____main____ 
Now with the object properly set up, we can reference to it using dot notation

```python
>>> from datetime import date
>>> t1 = Tweet('Giovanna', date(2022, 1, 18), 'Hello')
>>> t1.userid
'Giovanna'
>>> t1.created_at
datetime.date(2022, 1, 18)
>>> t1.content
'Hello'
>>> t1.likes
0
```

#### What Really Happens When We Create a New Object
Our initializer differs from a regular function in a few key ways
- An initializer always uses the parameter *self*
- By convention, we omit the type annotation of *self*, as the type should always be the class that it belongs to (ex. Tweet)

You may also notice that the return type of an initializer is always **None**, yet when we call the initializer it seems to return a new instance. This is because the __init__ only does 3 things.
1. Create a new Tweet object behind the scenes
2. call __init__ with the new object passed to the parameter *self*, along with the 3 arguments. (who, when, and what)
3. Return the new object, this step is where the object is returned and not a direct call from __init__

A class acts like a blueprint or a template, enforcing a common structure in all the data within the newly created type. 

### Defining a Class: Method
Now that we have our new data type initialized, we can write functions that take in tweets as arguments, or even create and return a new tweet. Below are two simple examples,
```python
def like(tweet: Tweet, n: int) -> None:
	""" Record the fact that <tweet> recieved <n> likes.

	precondition n >= 0
	>>> t = Tweet('Rukhsana', date(2022, 1, 16), 'Hey')
	>>> like(t, 3)
	>>> t.likes
	3
	"""
	tweet.likes += n

def retweet(new_user: str, tweet: Tweet, new_date: date) -> Tweet:
	""" Create a copy of the given tweet with the new user and date.

	The new tweet has 0 likes regardless of the number of likes on the original      tweet.
	>>> t1 = Tweet('Rukhsana', date(2022, 1, 16), 'Hey')
	>>> t2 = retweet('Calliope', t1, date(2022, 1, 18))
	>>> t2.userid
	'Calliope'
	>>> t2.created_at
	datetime.date(2022, 1, 18)
	t2.content
	'Hey!'
	>>> t2.likes
	0
	return Tweet(new_user, new_date, tweet.content)
```
While we can doing everything for our tweet class by writing functions, there are downside to doing so, as these functions are separate from the class itself, and must be imported by any program that wants to make use of them. 

## Methods
### Defining Methods Instead
Think back to how you used Python strings before you knew anything about classes.
```python
>>> word = 'supercalifragilisticexpeadalidocious'
>>> word.count('i')
6
```
It would be nice to use Tweet in this way, but we cannot in its current state, but we can change it. We can move the function inside of the class, making them methods (the term for functions in a class)

for example, our like function above could be a method in Tweet with this code
```python
class Tweet:
	...
	def like(self, n: int):
		self.likes += n
```
Notice how we added the parameter self to access instance attributes, just like the initializer

We can call methods of the tweet class without importing them separately, just importing the class Tweet itself. We call it using the same dot notation we use to access objects.
```python
>>> from datetime import date
>>> tweet = Tweet('Rukhsana', date(2022, 1, 16), 'Hey')
>>> tweet.like(10) # dot notation!
>>> tweet.likes
10
```

We can also, just like the str example above, call the method directly just as we would any other question, these calls are equivalent.
```python
>>> word.count('i')
6
>>> str.count(word, 'i')
6
```

### Methods vs Function
We just saw that methods in python are just a special kind of function (defined within a class). 

How do we decide when to make something a function and when to make it a method? The main difference between them is their design. Methods are apart of the very definition of a class, bundled together, in contrast, function operate independently. While this may make function sound less useful since they require more work, they are better in short term fixes. 

When we develop classes we want to 'future proof' the class, where it is impossible to predict every single thing a person will want to use a class for. 

Here is where our rule of thumb emerges, where when we write a class we write methods for behaviours that we think will be useful for 'most' users of the class. We write functions for the operations that users of the class must implement themselves for their specific needs. This is a *design* choice, judgement is required.

### Special Methods
We say that the initializer is a special method, but they are not they only ones. The double underscores used indicate that they are a special method. There are also `__str__` which will be called any time we print an instance of a class. Like this!
```python
>>> print(t1)
Giovanna said "Hello" on 2022-01-18 (0 likes) 
```

Later in the course we will discuss the methods in further detail.

# Chapter 2.2 - Representation Invariants
We now understand how to define a class that bundles together pieces of data and how to manipulate it through methods and functions.
We will revisit our Twitter example to consider what writing the methods "sensibly" entails

## Documenting Rules with Representation Invariants 😞
Twitter imposes a 280 char limit on tweets, and if we want our code to be consistent with the rule we must both document it and make sure that every method enforces the rule. First we must formalize the notion of "rule". 
A **representation invariant** is a property of the instance attributes that every instance of a class must satisfy. Like the 280 char rule
We document representation invariants in the docstring of a class underneath its attributes. We prefer concrete expressions
```python
class Tweet
	"""A tweet, like in Twitter.

	=== Attributes ===
	userid: the id of the user who wrote the tweet.
	created_at: the date the tweet was written.
	content: the contents of the tweet.
	likes: the number of likes this tweet has received.

	=== Representation Invariants ===
	- len(self.content) <= 280
	"""
	# Attribute Types
	userid: str
	created_at: date
	content: str
	likes: int
```

Although this is a new definition, we have seen this before when we define the attribute types. But this we must enforce

## Enforcing Representation Invarients
Even though documenting **representation invariants** is essential, documentation is not enough. As the author of a class, you must ensure each method is consistent with the **representation invariants**.
In our Twitter code, we must identify which method(s) may require modification to ensure (len(self.content) <= 280) is enforced. Currently the initializer allows the user to create a Tweet object with any message they want, including one that exceeds the limit.
One approach we can do is to precess the initializer arguments so that the instance attributes are initialized to allowed values. For example, we might truncate a tweet message thats too long. 

```python
def __init__(self, who: str, when: date, what: str) -> None:
	""" Initialize a new Tweet.
	If <what> is longer than 280 char, only first 280 chars are stored.

	>>> t = Tweet('Rukhsana', date(2017, 9, 16), 'Hey!')
	>>> t.userid
	'Rukhsana'
	>>> t.content
	'Hey!'
	>>> t.likes
	0
	"""
	self.userid = who
	self.created_at = when
	self.content = what[:280]
	self.likes = 0
```
Another approach is to not change the code at all, but instead, specify a precondition

The only issue is that the precondition is something that we assume to be true, but this doesn't work for a **representation invariant** because we should be enforcing it ourselves.

## Another Example: Non-negativity Constraints
Look again at the attributes of `Tweet`. Another obvious invariant is that likes must be at least 0, one of our preconditions. 
Again we are forced to make a decision on how to handle this, we could impose another precondition and ask for `n >= 0`, or we could allow negative numbers as an input, but set `self.likes = 0` if the value falls below 0.

All of these options change the method's behaviour, so whatever we choose would change the method's documentation.

# Chapter 2.3 - Designing Classes
By now we have introduced 3 elements of a class
- Instance attributes (data)
- Methods (operations)
- Representation Invariants (properties)

Now that we understand the basic mechanics of classes, it's time to think about the design of classes. 
## Information Hiding
The fundamental themes of the Class Design Recipe are *design before coding and information hiding*. Just as a great deal of thought goes into specifying the purpose and behaviour of a function, so too do you have to think about the design of a class before implementing anything.

The relationship between author and client of a class plays a powerful and subtle role in class design. When we design a class we consider how another person would use it. We design classes to be used by others. One of the biggest desires other uses have is to use our class without having to know everything about it, and its the original programmers job to fufill this. 

**information hiding** is a fundament element of OOP. One of the biggest advantages of designing our code likes this is because after the initial implementation, we can feel free to modify it without disturbing the public interface. 

## Private-ness in Python
We've discussed the Class Design Recipe's emphasis on the distinction between private and public interface, and so far we've working on using documentation to define a clear interface. We'll discuss another important way to document the attributes and methods that we want to keep private, going over the pitfalls concerning the concept of "privateness" in Python

### Leading Underscores
An extremely common Python naming convention is to name anything that is consdiered private with a **leading underscore**. An underscore on an instance attribute indicates to a programmer writing client code that they shouldn't access the instance variable: They shouldn't use its value, and certainly shouldn't change it. 
We cannot only mark attributes as private, but methods as well. 

### Python's "we're all adults" Philosophy
In other programming languages  we declare restrictions on what attributes can be accessed outside the class and which cannot. Such as Java where attempting to modify an attribute that has been marked as private leads to an error and prevents the code from running at all. 

The Python language takes a different approach and gives programmers a great deal of freedom when writing code. It allows users to access private attributes and methods of classes from outside the class, although there are other language mechanisms that can enforce restrictions, they are simply we mechanisms that can be circumvented. 

Python's philosophy ensures that if someone wants to use your class, they are responsible for using it "properly." And should they not, it's their problem.

This doesn't mean we can abandon our efforts in distinguishing private and public attributes, it just means our documentation must be that much better. 

## Combining Classes: Composition
A class is almost never defined and used in isolation. Often there is a large collection of classes which are all related to one another. One fundamental type of relationship between calsses occurs when the instances of one class have an attribute which refer to one or more instances of the other class. 
A `user` object might have a list of `Tweet`s as an attribute.
```python
class User:
	""" A Twitter user.
	=== Attributes ===
	userid: the userid of this Twitter user.
	bio: the bio of this twitter user.
	tweets: the tweets that this user has made
	"""
	# Attributes types
	userid: str
	bio: str
	tweets: list[Tweet]
```

This type of relationship between classes is called **composition** and appears all the type in OOP. 
When two classes refer to instances of the other, that's composition.

It is also the case that two classes might be related in more than one way. For example, we could change Tweet so that it has an isntance attribute `user` of type `User`, rather than just a string for the user's id, 

## Modelling with Classes
A common programming task is to take an English description of a problem and design classes that model the problem. The main idea is that the class(es) should correspond to the most important noun(s) in the problem. 

### People
Lets try!
We'd like to create a simple model of a person. We want to keep track of a persons mood throughout the day and when they eat their favourite food, their mood changes. People can be capable of almost anything, but we'll just give them the ability to change their name and greet another person.
```python
class Person:
    """
    We want to keep track of a person's mood throughout the day.
    When they eat their favorite food, their mood changes.
    People can be capable of many things, but we'll just give them the ability
    to change their name and greet others.

    === Attributes ===
    name: The name of the user.
    age: The age of the user.
    mood: The current mood level of the user.
    fav_food: The user's favorite food.
    """
    def __init__(self, mood: int, age: int, name: str, fav_food: str) -> None:
        self.mood = mood
        self.age = age
        self.name = name
        self.fav_food = fav_food

    def feed(self, eat: str) -> None:
        """
        Feed the person with the specified food. If the food matches
        their favorite food, increase their mood. Otherwise, decrease it.
        """
        if eat == self.fav_food:
            self.mood += 1
        else:
            self.mood -= 1

    def greeting(self, name2: str) -> str:
        """
        Return a greeting message for another person.
        """
        return f"Hi {name2}, it's nice to meet you! I'm {self.name}."

    def __str__(self) -> str:
        """
        Return a string representation of the person.
        """
        return f"Hi, my name is {self.name}!"
```

(my attempt with chatGPT help, turns out when you create your methods you shouldn't say self.name, just saying str works and then you reference the self.name within the code)