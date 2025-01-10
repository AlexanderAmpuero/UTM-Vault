# Chapter 1.1 - Introduction
## Data
Data in python has 3 distinct types, id, type, and value
- the id is the unique identifier
- the type is the class of the object
- the value determines which operators can be used on it

```python
>>> id(3)
1635361280
>>> type(3)
<class 'int'>
>>> round(8.93)
9
```

## Variables
All programming languages use the concepts of variables. In python, a variable is not an object, but rather, an id tag that refers to an object that stores data. ex. str, tuple, int, etc.

```python
>>> cat = 'Gilbert'
>>> id(cat)
4348845448
>>> cat = 'Chairman Meow'
>>> id(cat)
>>> 4355636784
```

Objects have types but variables don't, stay with me now. When asking for the type of a variable, for example, 'Gilbert', we would get that its type is a str. But this is not the type of the variable, rather, it is the type of the object which the variable contains.

### Immutability and Aliasing
Data types within Python are either mutable, or immutable, some notable examples being integers, strings, and booleans which are all immutable.
This can often be confusing, imagine you create a variable 'man' such that 'man' = 576. It is impossible to change the 7 in the 576 without changing the id of the object, thus, the integer is immutable.
This concept can be contrasted with lists, where imagine you have 'man' = [1, 2, 3, 4]. You can modify this list such that 'man' = [1, 2, 4] and the list will have the same id, although its items are different, thus it is mutable.  

User defined classes, lists, and dictionaries are all mutable and it is important to consider tuples, as they are the immutable counterpart to a list. 

Aliasing is important because it allows us to create two variables which refer to the same object. These variables are aliases of one another.

```python
>>> x = [1, 2, 3]
>>> y = [1, 2, 3]
>>> z = x
>>> id(x) == id(z)
True
>>> id(x) == id(y)
False
```

By having the same id, it causes the both variables to effect one another without being directly related. This is because they share the same identification tag. This means that, 
if you set z[0] = -999. Then x[0] == -999 will be true due to the z code mutating x without ever mentioning it. This is aliasing.

It is important to note that aliasing also exists for immutable data types, such as tuples, but does not have the same cause/effect relationship due to its immutability.

#### Special Case with Immutable Objects
When dealing with immutable objects, such as integers, you can set multiple variables to the same object and this doesn't cause any harm due to the immutable nature of the object. This also means that the id's of the two objects are equal
Python takes this shortcut with any immutable type, but in some strange cases it does not. Don't worry about these. 

#### is vs ==
They are both True or False statements

== is a value equality, which means that it compares the values stored in the objects they reference. 
is is a identity equality, which compares the id(s) of the objects which the variables
reference. 

# Chapter 1.2 - Functions and Parameters
## Terminology
```python
def mess_about(n: int, s: str) -> None:
	message = s * n
	print(message)

if __name__ = '__main__':
	count = 13
	word = 'nonsense'
	mess_about(count, word)
```

In the above function, every variable within the parentheses of the first line is a parameter within the function 'mess_about'. When we call this function in the below if statement, there is an argument for each parameter called count and word. 

## How are Function Calls Tracked?
Python must keep track of functions that are running, this tracking system is called stack frame, or just 'frame' for short. 
Every time a function is called, the following occurs.
1. A new frame is create and given priority over any frames that may already exists. This pile of frames is called the call stack
2. Each parameter is defined within said frame
3. The arguments in the function are each evaluated from left to right order. Each parameter is evaluated and creates an id for an object, then each of these id's are assigned to their corresponding parameter.
4. Then the body of the function is executed
5.  If a variable is called in the body which does not already exist, python creates in and puts it within the top stack frame.
- We call these function created variables as 'local variables'
When the function is done executing, the parameter and local variables used are deleted. This means that if you try to refer to them outside of the function you will get an error.

Often times when working with functions, programmers create global variables which are used as a constant for the parameters much like an alias.

### Passing a Reference to an Immutable Object
An important aspect of function programming is that by referencing an immutable object and changing it within a function, there will be no effect outside the function unless there is a return statement. Otherwise, the function returns 'None'

Function are multi use, whether it be to mutate an object, perform an action, or return a value.

### Passing a Reference to a Mutable Object
```python
def emphasize(lst: list[str]) -> None:
	lst = lst + ['believe', 'me!']

if __name__ == '__main__':
	sentence = ['winter', 'is', 'coming']
	emphasize(sentence)
	print(sentence)
```

When running the above code involving the mutable list type, we find that the code prints. 
['winter', 'is', 'coming']
This is not ideal as our method of changing the reference variable was not successful in mutating the value.

```python
def emphasize(lst: list[str]) -> None:
	lst.extend(['believe', 'me!'])

if __name__ == '__main__':
	sentence = ['winter', 'is', 'coming']
	emphasize(sentence)
	print(sentence)
```

Instead, we should have used this code which correctly mutates the function. This works because rather than changing the reference variable, we change the contents of existing mutable variable. 

Regarding lists, when adding or altering a list by using another id, it creates a new id for the list, treating the original aliased list as an immutable object temporarily. 

#### Moral of the Story
When working with objects that contain references to other objects it gets tricky. the bottom line is that you must know which objects are mutable or immutable, at each level of their structure. (think functions and stack frames). Constructing memory model diagrams can help greatly. 

# Chapter 1.3 - The function Design Recipe

The Function Design Recipe exists as a method of approaching problem-solving in python. The recipe ensures that code is written after a docstring has been created such that all parties can understand the work.

## Preconditions
First, the docstring of a code is required as it does not only describe what a function does through text and examples, but, also outline the requirements necessary to use the function. 
An essential requirement is the type contract: this tells a user what type of argument to use for a function.

```python
def decrease_at(numbers: list[int]) -> int:
	'''Return the index of the first number that is less than its predecessor

	>>> decrease_at([3,6,9,12,2,1,8,5])
	4
	'''
```

A precondition of a function is extremely important sinc
e they tell you which way to use the function. They limit how a function can be used while allowing the user to express their arguments in a way that satisfies the properties of the function. 

Example: a binary search function. In this function the precondition is that the parameters require it to be given a sorted list, otherwise the search method would not work. Rather than programming the function to determine whether or not the list is already sorted, it is smarter to give a precondition as if you had implemented the sorted list checker, the code could be slower than a linear search. 

Key Note - It is smarter to give a precondition rather than constantly check edge cases

#### Moral of the Story
Smarter to specify the preconditions within the design of the function, services of the function, and restrictions within the function.

# Chapter 1.4 - Python Type Annotations

In most programming languages you cannot use a variable until its type has been declared, ex. int, float, list. Usually, this variables type can never change. Python takes a very different approach from other languages, where only objects have a type, and a variable can refer to any type of object. 

We need to be aware of the existing types we are using at any point in our code. It is good practise to document this, we will use Python's new type annotation syntax to do so. 

## Primitive Types
```python
int - 0, 148, -3
float - 4.53, 2.0, -3.49
str - 'hello world', ''
bool - True, False
None - None
```

Note - the None type is a bit special, as it is both a value and a type
## Compound Types
```python
list[T] - [1, 2, 3], list[int] - # A list whose elements are all of type T
dict[T1, T2] - {'a': 1, 'b': 2, 'c': 3}, dict[str, int] - # A dictionary whose keys are of type T1, and values of type T2
tuple[T1, T2, ...] - ('hello', True, 3.4), tuple[str, bool, float] - # A tuple whose first element has type T1, and second has type T2, etc. 
```

We can nest these compound expressions with either a compound or primitive type
## Four Advanced Types
All four of these advanced types are imported from the "typing" module
### Any

```python
from typing import Any

# This function could return a value of any type
def get_first(items: list) -> Any:
	return items[0]
```

Often times beginners get lazy with their type annotations, tending to write "Any" even when a more specific type is appropriate. While this can satisfy PyCharm or PyTa, the overuse of the "Any" type can completely defeat the purpose of type notations. We should prioritize using specific type annotations
### Union
We sometimes want to express in type notation that a value could be one of two types; for example, we might say that a function can take in either an integer or a float. To do so, we use the union type

```python
from typing import Union

def cube_root(x: Union[int, float]) -> float:
	return x ** (1/3)
```

The above code shows that the input type can be either an integer or a float
### Optional
One of the most common uses of a "union type" is to say that a value could be a certain type, or None. For example, we might say that a function returns an integer or None, depending on some success or failure condition. Rather than using the Union[int, None], there is a slightly shorter version from the typing module called optional. The type expression Optional[T] is equivalent to Union[T, None] for all type expressions T. Here is an example

```python
from typing import Optional

def find_pos(numbers: list[int]) -> Optional[int]:
	"""Return the first positive number in the given list.

	return None if no numbers are positive
	"""
	for n in numbers:
		if n > 0:
			return n
```

### Callable
Finally, we sometimes need to express that the type of a parameter, return value, or instance attribute is itself a function. To do so, we use the Callable type from the typing module. This type takes two types, the first type is a list of input types, the second is a output return type.
ex. Callable[[int, str], bool] is a type expression which can take either a int or a string, and return a boolean.
```python
from typing import Callable

def compare_nums(num1: int, num2: int, comp: Callable[[int, int], bool]) -> int:
	if comp(num1, num2):
		return num1
	else:
		return num2

def is_twice_as_big(num1: int, num2: int) -> bool:
	return num1 >= 2 * num2

>>> compare_nums(10, 3, is_twice_as_big)
10
>>> compare_nums(10, 6, is_twice_as_big)
6
```

## Annotations
### Annotating Functions

```python
def can_divide(num, divisor):
	"""Return whether num is evenly divisible by divisor."""
	return num % divisor == 0
```

The above function takes in two integers and returns a boolean. We annotate the type of function by specifying the parameters with a colon. Like this.

```python
def can_divide(num: int, divisor: int):
```
We must also annotate the return type of the function by using an arrow at the ending of the close parenthesis
```python
def can_divide(num: int, divisor: int) -> bool:
```

Below, you can find the proper annotations

```python
def split_numbers(numbers: list[int]) -> tuple[list[int], list[int]]
	"""Return a tuple of lists, where the first list contains the numbers that       are >= 0, and the second list contains the numbers that are < 0"""

	pos = []
	neg = []
	for n in numbers:
		if n >= 0:
			pos.append(n)
		else:
			neg.append(n)
	return pos, neg
```

### Annotating Instance Attributes
When annotating the instance attributes of a class, we list each attribute along with its type directly in the body of the class. We list these at the very top of the class, after the class docstring and before any methods

```python
class Inventory:
	""" The inventory of a store.

	Keeps track of all of the items available for sale in the store

	Attributes:
	size: the total number of items available for sale
	items: a dictionary mapping an id number to a tuple with the item's              descirption and number in stock
	"""
	size: int
	items: dict[int, tuple[str, int]]

	... # Methods omitted
```

### Annotating Methods
1. By convention, we do not annotate the first parameter "self" where its type is always understood to be the class that this method belongs to
2. Sometimes we need to refer to the class itself, because it is the type of some parameter or the return type of a method. Due to python, we can only do so by including a special import statement at the very top of the python file

Ex.
```python
# This is the special import we need for the class type annotations.
from __future__ import annotations

class Inventory:

	# The type of self is omitted
	def __init__(self) -> None
		...
	def add_items(self, item: str, quantity: int) -> None:
		...
	def get_stock(self, item: str) -> int:
		...
	def compare(self, other: Inventory) -> bool:
		...
	def copy(self) -> Inventory
		...
	def merge(self, others: list[Inventory]) -> None:
		...
```

# Chapter 1.5 - Testing Your Work

The last and most helpful step of the Function Design Recipe is to test your code. We will discuss the various strategies for testing code. 

## Doctests: Basic Examples in Docstrings
First we will begin by using the python library "doctest", which looks for examples in the docstrings of the code and converts the to automatically runnable tests. To do so, you must add the following code to the bottom of any python file.
```python
if __name__ == '__main__':
	import doctest # import the doctest library
	doctest.testmod() # run the tests
```

Then when you run the file, all of the doctest examples are automatically run, and you recieve reports on which tests fail.

## Creating Test Suites with Pytest
The problem with doctests and putting examples in our docstrings is that we can't include all of the test cases we want without making the docstring way to long.

While you should continue to use basic doctests for the reader, in this course we will primarily use pytest. This allows us to write our tests in a separate file. You will see an example in your first lab.

- Each function whose name starts with "test" is a separate test, running independently and in random order
- Tests use the "assert" statement as the actual action that verifies the correctness of your code. The "assert" statement is used as follows.
```python
assert <expression>
```
The "expression" should be a boolean expression that tests something about your function. We say that an assertion succeeds (or passes) when it expression evaluates to True, and it fails when its expression evaluates to False.
A single test function in pytest can contain multiple assert statements, if a single statement fails the whole thing fails.
### Choosing Test Cases
Earlier we said that keeping our tests cases in a seperate file would enable us to write many tests, but it is important to write test cases which capture every possible scenario in as little assertions as possible. 
A good rule of thumb is to structure your tests around *properties of the inputs*. for example,
Integer, lists, and strings

### Property-Based Testing
The kinds of tests we've discussed so far involve defining input-output pairs: for each test, we write a specific input to the function we're testing, and then use 'assert' statements to verify the correctness of the corresponding output. These tests have the advantage that writing any one individual test is usually straightforward, but the disadvantage that choosing and implementing test cases can be challenging and time consuming.
This is the usual method of testing but to save time, we can use property-based testing
These tests have the advantage of a broad range of inputs in a short amount of code.

These tests are straightforward but it is often difficult to specify exactly what the output should be. 

Property based tests use assert statements to check for properties that the function tested should satisfy. In the simplest case, these are properties that every output of the function should satisfy.

Assertion Examples
- The type of the output: "the function str should always return a string"
- Allowed values of the output: "the function len should always return integer that is greater than or equal to 0"
- Relationships between the input and output: "the function max(x, y) should return something that is greater than or equal to both x and y"

These properties may seem strange because they do not capture precisely what each function does, for example, str should not just return any string. The challenge with property-based testing is to come up with good properties which narrow down as much as possible involving the behaviour of the function being tested.

#### Moral of the Story
ideally we use all three of these types of testing in combination:
- doctests
- test-suites
- property-based tests

# 1.6 - Choosing Test Cases
Testing is important and there are various strategies for any form of testing, an example can be seen below.

Imagine if Python didn't have the max function, and we are writing a function to find the largest element in a list of integers. Suppose that we have tested the function on the following test cases, and that it passes them all:

![[Screenshot 2025-01-05 at 9.55.06 PM.png]]
Would you say that this function works? No, it only checks 7 cases. even if it passes 1000 test cases,  you should be skeptical. How can we be sure the tests don't omit a scenario that could cause failure?

The issues is that we must ensure the code works in all cases where there are too many cases to test. 

## Making a Convincing Argument
We may not be able to test every case, but we can divide and conquer the possible calls to the function.
If we choose the categories well, it will be reasonable to assume that the tested calls apply to all the calls in the category.

## How to Choose the Relevant Properties
This kind of arguments depends heavily on the catagories, we base the properties on the potential inputs. Here are some potential properties
- the size of an object
- the position in a ordered sequence
- the prescience of duplicates
- the value of an integer
- the value of a string
- the location of a whitespace

Not all of these properties are relevant to any particular function, but they often apply. Judgement is always required when assessing these properties and attempting to choose the appropriate test cases.

# 1.7 Introduction to Property-Based Testing
Hypothesis is a Python testing library that we'll use occasionally in this course for exercises and assignments. It should already be installed on your device. 
When writing tests we try to identify key properties on the input to the functions being tested. We then pick representative inputs that meet these properties, using them to write tests. When analyzing the relationship between the input and output which applies to all possible inputs, we call this property-based testing which is most famously used from the hypothesis library.

## An Example
Let's see a concrete example of what these property tests might look like, consider the following function.
```python
def insert_after(lst: list[int], n1: int, n2: int) -> None:
	"""After each occurence of <n1> in <lst>, insert <n2>
	>>> lst = [5, 1, 2, 1, 6]
	>>> insert_after(lst, 1, 99)
	>>> lst
	>>> [5, 1, 99, 2, 1, 99, 6]
	"""
```

We'll test two properties of this function.
1. 'insert_after' always returns 'None'
2. 'insert_after' increases the length of 'lst' by the number of times that 'n1' occurs in that list
```python
from hypothesis import given
from hypothesis.strategies import integers, lists

from insert import insert_after

@given(list(integers()), integers() integers())
def test_returns_none(lst: list[int], n1, n2: int) -> None:
	"""Test that insert_after always returns None.
	"""
	assert insert_after(lst, n1, n2) is None
```

The test case (test_returns_none) is preceded by the line
@given(list(integers()), integers() integers()); what this line does is tell hypothesis to generate "random" inputs of the given types: a list of integers, and then two other integers. These values are then passed to the test function, which then simply calls insert_after on them, and checks that the output is None.
The interesting part is that the @given statement doesn't just generate one set of random inputs, it generates dozens or even hundred depending on how hypothesis is configured.
We call input specifiers like integers() or lists() a strategy.

## A More Complex Property
Although the previous test seemed straight-forward, it is not. Since a property test a Python function, we can write pretty complex tests using all our Python knowledge.
```python
@given(lists(integer()), integers(), integers())
def test_new_item_count(lst: list[int], n1: int, n2: int) -> None:
	"""Test that the correct number of items is added.
	"""
	num_n1_occurences = lst.count(n1)
	original_length = len(lst)
	insert_after(lst, n1, n2)

	final_length = len(lst)

	assert final_length - original_length == num_n1_occurences
```

#### Further Reading
If you want to learn more, you can find further information about Hypothesis in the official Hypothesis Python documentation because we are only scratching the surface




