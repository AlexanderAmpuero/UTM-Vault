---
tags:
  - csc148
---

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

## From Lecture
```python
class Tweet:
	"""A tweet, like in Twitter.
	=== Attributes ===
	content: the contents of the tweet.
	userid: the id of the user who wrote the tweet.
	created_at: the date the tweet was written.
	likes: the number of likes this tweet has received.
	"""
	content: str
	userid: str
	created_at: date
	likes: int
	def __init__(self, who: str, when: date, what: str) -> None:
		"""Initialize a new Tweet.
		"""
		
	def like(self, n: int) -> None:
		"""Record the fact that this tweet received <n> likes.
		These likes are in addition to the ones <self> already has.
		"""
		
class User:
	"""A Twitter user.
	=== Attributes ===
	userid: the userid of this Twitter user.
	bio: the bio of this Twitter user.
	tweets: the tweets that this user has made.
	"""
	# Attribute types
	userid: str
	bio: str
	tweets: list[Tweet]
	_follows: list[User]
	followers: list[User]
	
	def __init__(self, id_: str, bio: str) -> None:
		"""Initialize a new user with the given id and bio.
		The new user initially has no tweets.
		"""
		self.userid = id_
		self.bio = bio
		self.tweets = []
		self._follows = []
		self.followers = []
		

	def tweet(self, message: str) -> None:
		"""Record that this User made a tweet with the given content.
		Use date.today() to get the current date for the newly-created tweet.
		"""
		t = Tweet(self.user, date.today(), message)
		self.tweets.append(t)

	def new_follower(self, other_user: User)
		"""
		otherguy = User("bob", "likes cheese")
		mainguy = User("tim", "loves cheese")
		mainguy.new_follower(otherguy)
		>>> mainguy._follows
		otherguy
		>>> otherguy.followers
		mainguy
		"""
		self._follows.append(other_user)
		other_user.followers.append(self)
```

# Chapter 2.4 - Inheritance: Introduction and Methods
In the next few chapter we will be discussing inheritance, a relationship between two classes

### Consider A Payroll System
Suppose we are designing an application to keep track of a company's employees. 
We want to include those who are paid based on annual salary or hourly wages. We might define them as separate types so that they can have different attributes.
for example, a salaried employee has a salary to be stored, but the hourly wage working only has their wage. These classes could also have different methods for the way their pay is calculated.
Since they both have so much in common, we can use **inheritance**!

## Inheritance to the Rescue!
A better design would "factor out" the things that are common to all employees and write them once. This is what inheritance allows us to do.
- We define a *base class* which includes the functionality that are common to all employees
- we define a *sub class* for each specific 
- Terminology note: if class `B` is a subclass of class `A`, we also say that `A` is a _superclass_ of `B`

```python
class Employee: # base class
	pass

class SalariedEmployee(Employee): # subclass of employee
	pass

class HourlyEmployee(Employee): # subclass of employee
	pass
```

The terminology we will use is base class and sub class, or parent class and child class

## Defining Methods in a Base Class
Now we're going to fill out our classes, to keep it simple, we'll only use one method per class.
All our method will do is print out when and how much the employee was paid.

```python
class Employee:
    """An employee of a company.
    """

	def get_monthly_payment(self) -> None:
		"""return how much this employee makes in a month
		"""
		pass

    def pay(self, pay_date: date) -> None:
        """Pay this Employee on the given date and record the payment.

        (Assume this is called once per month.)
        """
        payment = self.get_monthly_payment()
        print(f'An employee was paid {payment} on {pay_date}.')
```

Now that the method `pay` is complete, we can move onto the next problem of figuring out `get_monthly_payment`, which has the same problem of using something different for every employee.
We are going to leave the incomplete `get_monthly_payment` method in the `Employee` class, because it defines part of the interface that every type of `Employee` object needs to have. Subclasses will inherit this incomplete method, which they can redefine as appropriate. We’ll see how to do that shortly.
The purpose of writing all of this *incomplete* code within the Employee class exists for the purpose of making our next sub classes far easier.

## Making the Base Class Abstract
Because the `Employee` class has a method with no body, client code should not make instances of this incomplete class directly. We do two thinks to make this clear
1. Change the body of the incomplete method so that it simply raises a `NotImplementedError`We call such a method an **abstract method**, and we call a class which has at least one abstract method an **abstract class**.
2. Add a comment to the class docstring stating that the class is abstract, so that anyone writing client code is warned not to instantiate it. 
Here is the complete definition of our class
```python
class Employee:
    """An employee of a company.
    """

	def get_monthly_payment(self) -> None:
		"""return how much this employee makes in a month
		"""
		raise NotImplementedError

    def pay(self, pay_date: date) -> None:
        """Pay this Employee on the given date and record the payment.

        (Assume this is called once per month.)
        """
        payment = self.get_monthly_payment()
        print(f'An employee was paid {payment} on {pay_date}.')
```

It is possible for client code to ignore the warning an instantiate this class as python doesn't stop it. Look what happens when we try to call one of the unimplemented methods on the object!

```python
>>> a = Employee()
>>> a.get_monthly_payment() # abstract method
Traceback...
NotImplementedError
>>> a.pay(date(2018, 9, 30)) # This method calls the abstract helper
Traceback...
NotImplementedError
```

## Subclasses Inherit from the Base Class
Now let's fill in class `SalariedEmployee`, which is a **subclass** of Employee. Very importantly, all instances of SalariedEmployee are also instances of `Employee`. We can verify this using the build-in function `isinstance`.
```python
>>> # Here we see what isinstance does with an object of a simple built-in type.
>>> isinstance(5, int)
True
>>> isinstance(5, str)
False
>>> # Now let's check how it works with objects of a type that we define.
>>> fred = SalariedEmployee()
>>> # fred's type is as we constructed it: SalariedEmployee.
>>> # More precisely, the object that fred refers to has type SalariedEmployee.
>>> type(fred)
<class 'employee.SalariedEmployee'>
>>> # In other words, the object is an instance of SalariedEmployee.
>>> isinstance(fred, SalariedEmployee)
True
>>> # Here's the important part: it is also an instance of Employee.
>>> isinstance(fred, Employee)
True
```

Python "knows" that `fred` is an instance of `Employee`, this object will have access to all methods of `Employee`! We say that `fred` **inherits** all of the `Employee` methods. So even if `SalariedEmployee` remains an empty class, its instances can still call the methods `get_monthly_payment` and `pay`, because they are inherited.

```python
class SalariedEmployee(Employee):
	pass

>>> fred = SalariedEmployee()
>>> # Fred inherits Employee.get_monthly_payment, so we can call it
>>> fred.get_monthly_payment()
Traceback...
NotImplementedError
```

## Completing the Subclass
Our `SalariedEmployee` and `HourlyEmployee` subclasses each inherit the `pay` and `get_monthly_payment` methods. The `pay` method is already complete, but the `get_monthly_payment` method needs a new definition that doesn't raise an error.
We can do this by defining the method again in the subclass, we say that this new method definition overrides the inherited definition

```python
class SalariedEmployee(Employee):
    def get_monthly_payment(self) -> float:
        # Assuming an annual salary of 60,000
        return round(60000.0 / 12.0, 2)

class HourlyEmployee(Employee):
    def get_monthly_payment(self) -> float:
        # Assuming a 160-hour work month and a $20/hour wage.
        return round(160.0 * 20.0, 2)

>>> fred = SalariedEmployee()
>>> fred.get_monthly_payment()
5000.0
>>> jerry = HourlyEmployee()
>>> jerry.get_monthly_payment()
3200.0
```

We now have a working version of all 3 of the classes. 

## How Python Resolves a Method Name
When working with the classes we've made, we have the `fred.get_monthly_payment`, which could refer to the salary, employee, or hourly wage methods we have created. To understand which method Python uses, we must understand pythons **method resolution**.

This is how it works: whenever code calls `a.myMethod()`, Python determines what `type(a)` is and looks in that class for `myMethod()`. If `myMethod` is found in the class, it's called, otherwise Python next searches for `myMethod()` as a superclass of the type of `a`, and then that superclass, etc. 
It continues this until it finds the definition of `myMethod()`, otherwise it raises an `AttributeError`

In the case of `fred.get_monthly_payment`, it goes to `SalariedEmployee` because `type(fred)` 

### A Strange Case
What if we called the `fred.pay(date(2018, 9, 30))`. The value of type(fred) is `SalariedEmployee`, but class `SalariedEmployee` does not contain a `pay` method. This means that Python must next check its superclass `Employee`, which does contain a `pay` method, causing it to be called. But it only calls the `pay` method for this call, when calling something again, such as the `get_monthly_payment` it would take you to the `SalariedEmployee` since that's where the Python starts.

Don't forget the NotImplementedError

# Chapter 2.5 - Inheritance: Attributes and Initializers
We will return and use the payroll code we wrote earlier, and generalize it from hard-coded values to instance attributes. This will allow us to customize individual employees with their own annual salaries or hourly wages.

## Documenting Attributes
Just as the base class contains abstract and regular methods that all subclasses have in common, the base class also documents attributes that all subclasses need to have in common. both are a fundamental part of the public interface of a class

We chose earlier that we would need to record an id and name for every employee, heres how we'll do it beginning with the base class.

```python
class Employee:
	""" An employee of a company.
	=== Attributes ===
	id_: this employee's ID number
	name: This employee's name
	"""
	id_: int
	name: str
```

## Define an Initializer in the Abstract Superclass
Even though abstract classes should not be instantiated directly, we provide an initializer in the superclass to initialize the common attributes.

```python
class Employee:
	def __init__(self, id_: int, name: str) -> None:
		""" Initialize this employee """
		self.id = id_
		self.name = name

	def get_monthly_paynment(self) -> float:
		""" Return the amount that this employee should be payed in a month
		round the amount to the nearest cent.
		"""
		raise NotImplementedError

	def pay(self, date: str) -> None:
		""" Pay this Employee on the given date and record the payment
		assume this is called once per month
		"""
		payment = self.get_monthly_payment()
		print(f"An employee was paid {payment} on {date}.")
```

## Inheriting the Initializer in a Subclass
Because this initializer is a method, it is automatically inherited by all `Employee` subclasses just as, for instance, `pay` is. 

To inherit the initializers from the Employee class, we have two options:
```python
class SalariedEmployee(Employee):
    def __init__(self, id_: int, name: str, salary: float) -> None:
        self.id_ = id_         # Copied from Employee.__init__
        self.name = name       # Copied from Employee.__init__
        self.salary = salary   # Specific to SalariedEmployee

class HourlyEmployee(Employee):
    def __init__(self, id_: int, name: str, hourly_wage: float,
                 hours_per_month: float) -> None:
        self.id_ = id_                          # Copied from Employee.__init__
        self.name = name                        # Copied from Employee.__init__
        self.hourly_wage = hourly_wage          # Specific to HourlyEmployee
        self.hours_per_month = hours_per_month  # Specific to HourlyEmployee
```
This solution is not elegant though, and could be far simpler. We just need to use `Employee.__init__` as a helper method. Rather than overriding the method, we will extend it.
```python
class SalariedEmployee(Employee):
    def __init__(self, id_: int, name: str, salary: float) -> None:
        # Note that to call the superclass initializer, we need to use the
        # full method name '__init__'. This is the only time you should write
        # '__init__' explicitly.
        Employee.__init__(self, id_, name)
        self.salary = salary
```
In the subclasses we can't forget to document each instance attribute and declare its type, below you can see the completed subclasses.

```python
class SalariedEmployee(Employee):
    """
    === Attributes ===
    salary: This employee's annual salary

    === Representation invariants ===
    - salary >= 0
    """
    id_: int
    name: str
    salary: float

    def __init__(self, id_: int, name: str, salary: float) -> None:
        # Note that to call the superclass initializer, we need to use the
        # full method name '__init__'. This is the only time you should write
        # '__init__' explicitly.
        Employee.__init__(self, id_, name)
        self.salary = salary

    def get_monthly_payment(self) -> float:
        return round(self.salary / 12, 2)


class HourlyEmployee(Employee):
    """An employee whose pay is computed based on an hourly rate.

    === Attributes ===
    hourly_wage:
        This employee's hourly rate of pay.
    hours_per_month:
        The number of hours this employee works each month.

    === Representation invariants ===
    - hourly_wage >= 0
    - hours_per_month >= 0
    """
    id_: int
    name: str
    hourly_wage: float
    hours_per_month: float

    def __init__(self, id_: int, name: str, hourly_wage: float,
                 hours_per_month: float) -> None:
        Employee.__init__(self, id_, name)
        self.hourly_wage = hourly_wage
        self.hours_per_month = hours_per_month

    def get_monthly_payment(self) -> float:
        return round(self.hours_per_month * self.hourly_wage, 2)
```
Above you can see they both inherit the id_ and name properties. 

## Subclasses Inherit Methods, Not Attributes
It may seem that our two subclasses have "inherited" the attributes documented in the `Employee` class. But remember that a type annotation does not create a variable

```python
>>> fred = SalariedEmployee(99, 'Fred Flintstone', 60000.0)
>>> fred.name
'Fred Flintstone'
```
In the above code we see that the `fred` has a `name` attribute, but this doesn't belong to `SalariedEmployee`. This is because the `SalariedEmployee` initializer explicitly calls the `Employee` initializer, which then can initialize the attribute `name`. 
A superclass is *not* called automatically when a subclass instance is created. If we remove this call, we see it doesn't work because id_ and name initializers are missing.
```python
class SalariedEmployee(Employee):
    def __init__(self, id_: int, name: str, salary: float) -> None:
        # Superclass call commented out:
        # Employee.__init__(self, id_, name)
        self.salary = salary


>>> fred = SalariedEmployee('Fred Flintstone')
>>> fred.name
AttributeError
```

## Initializers with Different Signitures
Notice that the signatures for `Employee.__init__` and `SalariedEmployee.__init__` are different, as the `SalariedEmployee.__init__` has the addition parameter, salary. This makes sense as we should be able to configure each salaried employee with their own salary, and its irrelevant for those whose pay isn't salary based.

Because abstract classes aren't meant to be instantiated directly, their initializers are considered *private*. This, they can be freely overridden to have their signatures changed in each subclass. This gives a programmer flexibility, but subclass initializers should always call the initializer of their superclass.

Python allows us to change the signature of *any* method we override, not just `__init__`. We will discuss this later and in this course, we'll use inheritance to define `interfaces` that your subclasses should implements

### Moral of the Story!
We're going to be using the `.__init__` method a ton when doing inheritance, as far as I know, it is the only way inheritance is achieved (I'll probably learn more about it later though). I'll give an example below.
```python
class HourlyEmployee:
	def __init__(self, id_: int, name: str, hourly_wage: float,
		        hours_per_month: float) -> None:
	    Employee.__init__(self, id_, name)
	    self.hourly_wage = hourly_wage
	    self.hours_per_month = hours_per_month
```

# Chapter 2.6 - Inheritance: Thoughts on Design
Now that we understand the basics of inheritance, lets revise our knowledge.
## Four Things we can do with an Inherited Method
When a subclass inherits a method from its superclass, there are four things we can choose to do in the subclass

### 1. Simply Inherit an Implemented Method
If a method has been implemented in the superclass and its behaviour is appropriate for the subclass, then we can simply use this behaviour by choosing not to override the method. 
Of course, never do this when the method is abstract, because a subclass must override every abstract method to implement it properly
### 2. Override an Abstract Method to Implement It
When a method has not been implemented in the superclass (its body is just `raise NotImplementedError`), the method *must* be overridden in the subclass in order to provide an implementation. For example, `SalariedEmployee` and `HourlyEmployee` must both implement the abstract `get_monthly_payment` method, they cannot simply inherit it. 
### 3. Override an Implemented Method to Replace It
If a method *has* been implemented in the superclass, but the subclass requires a different behaviour, the subclass can override the method and provide a completely different implementation. This is something we haven't seen, but it's quite simple. 
```python
class SalariedEmployee(Employee):
    def get_monthly_payment(self) -> float:
        # Assuming an annual salary of 60,000
        return round(60000 / 12, 2)

    def pay(self, pay_date: date) -> None:
        print('Payment rejected! Mwahahahaha.')

>>> fred = SalariedEmployee()
>>> fred.pay(date(2017, 9, 30))
Payment rejected! Mwahahahaha.
```
In the above code we can see that instead of using the inherited `pay` method that it has from `Employee`, it overrode it with its own pay function
### 4. Override an Implemented Method to Extend It
Sometimes we want the behaviour that was defined in the superclass, but we want to add some other behaviour. Doing this is called extended the inherited behaviour. We have witnessed this in the initializers for our payroll system in previous chapters. The `Employee` initializer takes care of instance attributes, and the subclasses add more attributes.

Here you can see an example of this below.
```python
class SalariedEmployee(Employee):
    def pay(self, pay_date: date) -> None:
        Employee.pay(self, pay_date)  # Call the superclass method as a helper.
        print('Payment accepted! Have a nice day. :)')

>>> fred = SalariedEmployee()
>>> fred.pay(date(2017, 9, 30))
An employee was paid 3200 on September 30, 2017.
Payment accepted! Have a nice day. :)
```

## Using Inheritance to Define a Shared Public Interface
Our use of inheritance allows client code to do the same thing for all types of employees. Here's an example where we iterate over a list of employees and call method `pay` on each, without regard to what kind of `Employee` each one is. 

In other words, the client can write whatever code they wish to an interface, and add their own subclasses that we haven't even thought of!

We say that the `Employee` class represents the *shared public interface* of classes `SalariedEmployee` and `HourlyEmployee`. The public interface of a class is the way client code interacts with the methods and attributes of the code. 

We say that the `Employee` class is **polymorphic**, to signify that it can take various forms in its subclasses.

## Abstract Classes are Useful
The `Employee` class is abstract, and client code should never instantiate it. Does that mean it's useless? No, quite the opposite! We've already seen that it defines a *shared public interface* for the classes, supports polymorphism, and most important, lets the client add their own code in the future. 
The `Employee` abstract class is useful in a second way, it provides guidance to future programmers making subclasses

## When to use Inheritance
We've seen the many benefits of inheritance, however, inheritance isn't perfect in every situation. Don't forget the other kind of relationship between classes that we discussed a while ago, **composition.**  
A `person` object might have an attribute `car` which stores reference to the `car` object, in this case we'd want to use **composition**, as it allows the `person` to keep the car, without copying the information to other as inheritance would. 

For example, a person "has a" car. Inheritance is thought of as a "is a" relationship, whereas composition has a "has a" relationship.

When using inheritance, any change in a superclass affects all of the subclasses whichn can lead to unintended effects. To avoid this complexity, in this course we will stick to using inheritance in the traditional sense, as a *shared public interface*. Moreover, we will often prefer that a **subclass not change the public interface of a superclass at all!**
- No changing the interface by adding and removing parameters, or changing their types
- No adding new public methods or attributes to a subclass. (adding *private* attributes or methods is allowed)

You will learn a lot more about inheritance in CSC207, *Software Design*

### Moral of the Story
Composition uses a "has a" relationship instead of a "is a", which makes it superior is some instances. Also follow the green steps above, those are the ways inheritance will/can be used.

# Chapter 2.7 - The `object` Class and Python Special Methods
Now that we understand inheritance, we can gain a deeper understanding of some of Python's fundamental special methods.

## The `object` superclass
In our very first lecture we described every piece of data an an `object`, and have continued to use this term throughout this course. It turns out that "object" is not merely a theoretical concept, but is made explicit in the Python language. In Python we have a class called `object`, which is an *ancestor* of every other class, both build-in classes like `int` and user-defined classes like the `Employee` class we made in an earlier activity

## Inheriting Special Methods
This `object` class gives default implementations for many special methods we have seen before, this includes,
- `__init__` which creates instances of a class, every time we define `__init__` we are actually overriding the previous `object.__init__`
- `__str__`, which is called when you call either `str` or `print` on an object. The default implementation? Identifying the class name and a location in your computer's memory

```python
>>> class Donut:
...    pass
...
>>> d1 = Donut()
>>> print(d1)
<Donut object at 0x103359828>
```
- `__eq__`, whose implementation simply uses `is` to compare two objects

Keep in mind that even though these methods are called "special", overriding them in your classes works in the exact same was as other methods: simply define a method with the specific name and special method.
We've done this before in csc108 with `__str__`

### Moral of the Story
Special classes are objects too and we can manipulate them. These special objects are special and manipulating them can change behaviour, especially in inheritance.
Most importantly, the `object` superclass is the most superclass, the one above all. 