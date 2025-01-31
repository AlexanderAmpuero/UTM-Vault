---
tags:
  - csc148
---
# Chapter 3.1 - Introduction to Abstract Data Types
In the first few weeks we primarily developed classes and focused on implementation. However, we primarily spend our programming career in the opposite role: whenever you use one of Python's built-in functions or data structures, you only worry about what it does, not how it works. In other words. We are the client working with all of pythons built in classes and libraries.

Some concepts are so general and useful that they transcend any specific programming language, and that it Abstract Data Types, or ADT's. ADT's define some kind of data and the operation that can be performed with it. It is a pure interface without mention of implementation, making it **abstract**. In cotrast to this, a **data structure** is a concrete strategy for storing data, for example, a data structure could store the grades of a class on a series of activities.
ex. 
```python
grades = [['Bogdan', 78, 82],
		 ['Rutwa', 75, 84],
		 ['Marc', 80, 79],
		 ['Romina', 83, 88]]
# To know what each column is for, we can use another list
items = ['A1', 'midterm']
```

An alternative data structure is a dictionary of dictionaries
```python
g2 = {'A1': {'Bogdan': 78, 'Rutwa': 75, 'Marc': 80, 'Romina': 83},
      'midterm': {'Bogdan': 82, 'Rutwa': 84, 'Marc': 79, 'Romina': 88}}
```

You can likely come up with other ways to store the code, and may find it interesting to consider pros and cons of the two data structures above. 
The point of this exercise though, is that ADTs are fundamentally concerned with the *what*, what data is stored, and what we can do with this data. 
Data structures are concerned with the *how*, like how is that data stored and how do we implement methods that can operate on this data.
It is crucial to understand the differences, and it is vital for a programmer developing their abstract thinking. Separate the *what* fro the *how* and there are many benefits.

## Some Famous Abstract Data Types

- **Set**
    - Data: a collection of unique elements
    - Operations: get size, insert a value (without introducing duplicates), remove a specified value, check membership
- **Multiset**
    - Data: a collection of elements (possibly with duplicates)
    - Operations: same as Set, but the insert operation allows duplicates
- **List**
    - Data: an ordered sequence of elements
    - Operations: access element by index, insert a value at a given index, remove a value at a given index
- **Map**
    - Data: a collection of key-value pairs, where each key is unique and associated with a single value
    - Operations: lookup a value for a given key, insert a new key-value pair, remove a key-value pair, update the value associated with a given key
- **Iterable**
    - Data: a collection of values (may or may not be unique)
    - Operations: iterate through the elements of the collection one at a time.
*MIGHT HAVE TO MEMORIZE*

Although we have not discussed ADTs before, many of these data structures should be familiar. 

It is important to know that a `dict` itself is not a ADT, but we can categorize it as a implementation of the **Map ADT** 

A single ADT can be implemented by many different data structures. For example, although Python's `list` is a natural implementation of the List ADT, we could implement it instead with a `dict` in which each key is the index of its item. As seen below
```python
{0: 'hello', 1: 42, 2: 'goodbye'}
```

Every data structure can be utilized to implement multiple ADTs. For example, the Python `list` can be used to implement every ADT within itself. 

## The Value of Understanding Basic ADTs

We've said that each ADT is so general that it transcends basic languages and problems. While the data structures used to implement each ADT may differ from language to language, they remain the basics of everything. 

# Chapter 3.2 - Stacks and Queues
To round out our study of ADTs, we will discuss two new ADTs this week, the **Stack** and the **Queue**. Both of these ADTs store a collection of items and support operations. However, unlike **Set** or **Multiset**, where client code may specify which item to remove, Stacks and Queues remove and return their items in a fixed order.
This may seem restrictive and simplistic at first, but their power lies in their simplicity. Once we learn about them, we will be able to recognize them everywhere, and effectively communicate about them to any other Computer Scientist. 

## The Stack ADT
The **Stack** ADT is very simple. A stack contains 0 or more items. When you add an item, it goes to the "top" of the stack (We can this "pushing onto the stack). When you remove an item from the "top", its called "popping" and item from the Stack. The net effect is that the first item added to the stack is the last item removed. 
We call this behaviour, *Last-In-First-Out* or (LIFO). To summerize:
- **Stack**
    - Data: a collection of items
    - Operations: determine whether the stack is empty, add an item (_push_), remove the most recently-added item (_pop_)
    - The below example of a stack was implemented using the Python data structure: `list`. Here we've chosen the end of the list to represent the top of the stack, although it wasn't the only option. 
```python
class Stack:
    """A last-in-first-out (LIFO) stack of items.

    Stores data in first-in, last-out order. When removing an item from the
    stack, the most recently-added item is the one that is removed.
    """
    # === Private Attributes ===
    # _items:
    #     The items stored in the stack. The end of the list represents
    #     the top of the stack.
    _items: list

    def __init__(self) -> None:
        """Initialize a new empty stack.
        """
        self._items = []

    def is_empty(self) -> bool:
        """Return whether this stack contains no items.

        >>> s = Stack()
        >>> s.is_empty()
        True
        >>> s.push('hello')
        >>> s.is_empty()
        False
        """
        return self._items == []

    def push(self, item: Any) -> None:
        """Add a new element to the top of this stack.
        """
        self._items.append(item)

    def pop(self) -> Any:
        """Remove and return the element at the top of this stack.

        >>> s = Stack()
        >>> s.push('hello')
        >>> s.push('goodbye')
        >>> s.pop()
        'goodbye'
        """
        return self._items.pop()
```
Note: the visual order of the stack doesn't matter, its all about when it was added. 

## The Queue ADT
Another important ADT is **Queue**. Like a stack, a queue contains zero or more items. but items come out of a queue in the order in which they enter. In other words, a queue exhibits a *First-In-First-Out* behaviour (FIFO). 
We call adding an item to a queue an **enqueue** operation, and the removal of an item a **dequeue** operation. To summarize:
- **Queue**
    - Data: a collection of items
    - Operations: determine whether the queue is empty, add an item (_enqueue_), remove the least recently-added item (_dequeue_)

## Abstraction is Critical
While seemingly basic, abstraction is one of the most powerful concepts in computer science. An ADT is an abstraction so general that it transcends and programming language. 

Looking from outside the class setting, a programmer writing client code needs to understand only its public interface. This frees their attention and allows them to do what they want to do with the class and ignore everything about how to implement it. 
If a client creates a `Stack` object in their code, they know there are exactly 3 operations that can be performed on it (checking empty, pushing, popping). This largely reduces the cognitive load on a programmer. 

Looking within the class, the implementer has complete freedom to change implementation details with no effect on client code. This lets software to be redesigned or made more elegant without ruining the original developers ideas. We call this *plug-out, plug-in compatibility*

# Chapter 3.3 - Exceptions
Right now, our stack implementation raises an unfortunate error when client code calls pop on an empty `Stack`:
```python
>>> s = Stack()
>>> s.pop()
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "...", line 58, in pop
    return self._items.pop()
IndexError: pop from empty list
```
Let's look at some alternatives

## Alternative: Fail Silently
One simple improvement is to make the code "fail silently", making sure to document this behaviour in the method's docstring. 
```python
def pop(self) -> Any:
        """Remove and return the element at the top of this stack.

        Do nothing if this stack is empty.

        >>> s = Stack()
        >>> s.push('hello')
        >>> s.push('goodbye')
        >>> s.pop()
        'goodbye'
        """
        if not self.is_empty():
            return self._items.pop()
```
Because the client code in this case expects a value returned, it could use the "No return value" as a sign that something bad happened. However, this approach doesn't work for all methods; for example, `push` never returns a value, not even when all goes well. So fail silently would suck. And in `pop` which does return a value, we we treat `Nonne` as an indication of an error we can never allow client code to `push` the value `None`, and there may be clients who want to do that.

## Alternative: Raise A user-defined exception
A far better solution is to raise an error when something has gone wrong, so that the client code has a clear signal. We want the errors to be descriptive, yet not reveal any implementation details. We can do this easily by defining our own error by making a subclass called `Exception`. Here's an example for Stack errors
```python
class EmptyStackError(Exception):
    """Exception raised when calling pop on an empty stack."""
    pass
```
We'll call this a user-defined exception
Here's how we'll use `EmptyStackError` in our `pop` method

```python
def pop(self) -> Any:
    """Remove and return the element at the top of this stack.

    Raise an EmptyStackError if this stack is empty.

    >>> s = Stack()
    >>> s.push('hello')
    >>> s.push('goodbye')
    >>> s.pop()
    'goodbye'
    """
    if self.is_empty():
        raise EmptyStackError
    else:
        return self._items.pop()

>>> s = Stack()
>>> s.pop()
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "...", line 60, in pop
    raise EmptyStackError
EmptyStackError
```
When we want an `EmptyStackError` to happen, we construct an instance of that new class and `raise` it. We have seen this keyword before in the context of `NotImplementedError` from out classes chapter. 
What is important is that in the error it doesn't mention any of our implementation details, and specifies what the error was and how they messed up.
## Customizing the Error Message
One current limitation of the above approach is that simply the name of the error class is not enough to convey what the error was. We can change this my altering the `__str__` class that the method has
```python
class EmptyStackError(Exception):
    """Exception raised when calling pop on an empty stack."""

    def __str__(self) -> str:
        """Return a string representation of this error."""
        return 'You called pop on an empty stack. :('


>>> s = Stack()
>>> s.pop()
Traceback (most recent call last):
  File "<input>", line 1, in <module>
  File "...", line 60, in pop
    raise EmptyStackError
EmptyStackError: You called pop on an empty stack. :(
```

## Exceptions Interrupt the Normal Flow of Contol
The *Normal Flow of Control* in a program involves pushing a stack frame whenever a function is called, and popping the current (top) stack frame when we reach a `return` or reach the end of the function/method. When an exception is raised, something very different happens: immediately the function ends and its stack frame is popped. sending the exception back to *its* caller, and so on until the stack is empty. At that point an error message specifies why the exception is printed and the program stops.

In fact, when this happens, much more information is printed. For every stack frame that is popped, there was a function/method that had been running and was at a particular line. The output shows the line number and line of code. Here's an example. 
![[Pasted image 20250123155120.png]]
This function will fail because `clear_stack` removes everything from the stack, but answer tries to find the `second_from_top` which fails because a `pop` is being attempted on an empty list. When this failure occurs, this is returned.
![[Pasted image 20250123155417.png]]
We have seen this error before, and now we can use its "treasure trove" of information in our debugging process.

## Handling Exceptions More Elegantly

Now we are going to learn the try catch methods for better testing and exceptions. Catching an exception and taking an appropriate action instead of allowing your code to crash is a elegant way to handle errors and prevents users from seeing ugly code.

Consider a simple example of asking for input from the user in the form of an integer input value. and testing if that number is a divisor of 42. Here is how we can handle the code to ensure it is an integer.
```python
if __name__ == '__main__':
    option = 'y'
    while option == 'y':
        value = input('Give me an integer to check if it is a divisor of 42: ')
        try:
            is_divisor = (42 % int(value) == 0)
            print(is_divisor)
        except ZeroDivisionError:
            print("Uh-oh, invalid input: 0 cannot be a divisor of any number!")
        except ValueError:
            print("Type mismatch, expecting an integer!")
        finally:
            print("Now let's try another number...")
        option = input('Would you like to continue (y/n): ')
```

In the context of our stack code we can simply handle the `EmptyStackError` through a try-catch. We don't necessarily have to print a message to the user, although the below code will. Since we are choosing to print a message, we must change the return time from `str` to `Optional[str]`
```python
def second_from_top(s: Stack) -> Optional[str]:
    """Return a reference to the item that is second from the top of s.
    Do not change s.
    
    If there is no such item in the Stack, returns None.
    """

    hold2 = None
    try:
        # Pop and remember the top 2 items in s.
        hold1 = s.pop()
        hold2 = s.pop()
        # Push them back so that s is exactly as it was.
        s.push(hold2)
        s.push(hold1)
        # Return a reference to the item that was second from the top.
    except EmptyStackError:
        print("Cannot return second from top, stack empty") # bad practice
    return hold2
```
Note: this code is quite bad from the clients perspective because try-catch in this fashion will not show where the error occurs (what line and the line of code). Like we saw earlier.

# Chapter 3.4 - Analyzing Program Running Time
Here is an alternate way of implementing the Stack ADT based on lists, using the *front* of the list to represent the top of the stack.
```python
class Stack2:
    """Alternate stack implementation.

    This implementation uses the *front* of the Python list to represent
    the top of the stack.
    """
    # === Private Attributes ===
    # _items:
    #     The items stored in the stack. The front of the list represents
    #     the top of the stack.
    _items: list

    def __init__(self) -> None:
        """Initialize a new empty stack."""
        self._items = []

    def is_empty(self) -> bool:
        """Return whether this stack contains no items.

        >>> s = Stack()
        >>> s.is_empty()
        True
        >>> s.push('hello')
        >>> s.is_empty()
        False
        """
        return self._items == []

    def push(self, item: Any) -> None:
        """Add a new element to the top of this stack."""
        self._items.insert(0, item)

    def pop(self) -> Any:
        """Remove and return the element at the top of this stack.

        Raise an EmptyStackError if this stack is empty.

        >>> s = Stack()
        >>> s.push('hello')
        >>> s.push('goodbye')
        >>> s.pop()
        'goodbye'
        """
        if self.is_empty():
            raise EmptyStackError
        else:
            return self._items.pop(0)
```
Even though this implementation is conceptually the same as the first one (this uses the front of the stack, the previous used the end of the stack). It turns out their runtime is quite different

## A Simple Time Profiler
By making use of a built-in Python library called `timeit`, we can easily get rough estimates of how long our code takes to run. The key function we import is called `timeit`, which takes in a piece of Python code and return a float representation how long it took to execute it. 
```python
def push_and_pop(s: Stack) -> None:
    """Push and pop a single item onto <stack>.

    This is simply a helper for the main timing experiment.
    """
    s.push(1)
    s.pop()


if __name__ == '__main__':
    # Import the main timing function.
    from timeit import timeit

    # The stack sizes we want to try.
    STACK_SIZES = [1000, 10000, 100000, 1000000, 10000000]
    for stack_size in STACK_SIZES:
        # Uncomment the stack implementation that we want to time.
        stack = Stack()
        # stack = Stack2()

        # Bypass the Stack interface to create a stack of size <stack_size>.
        # This speeds up the experiment, but we know this violates encapsulation!
        stack._items = list(range(stack_size))

        # Call push_and_pop(stack) 1000 times, and store the time taken in <time>.
        # The globals=globals() is used for a technical reason that you can ignore.
        time = timeit('push_and_pop(stack)', number=1000, globals=globals())

        # Finally, report the result. The :>8 is used to right-align the stack size
        # when it's printed, leading to a more visually-pleasing report.
        print(f'Stack size {stack_size:>8}, time {time}')
```
Running this code on both of our stacks we've made this chapter illustrates a stark different between the two classes. While `Stack` seems to take the same amount of time no matter how many items are in the stack, `Stack2` 's time seems to grow with the number of items on the stack. In fact, the amount of time required in a `Stack2` operation is roughly proportional to the size of the stack, while the amount of time required in `Stack` is *independent* of the size of the stack!

### Memory Allocation for Lists in Python
To understand why there's such a dramatic difference between the two stacks, we need to understand how Python stores lists in memory. 
Recall that a variable in Python store a *reference* to an object. A Python list is a special type of object that contains an ordered sequence of references to other objects. We call them elements of the list. These references are stored in consecutive blocks of memory in python, this is what makes accessing list elements so fast. We simply get the i-th list item, which is done by finding its address in the computer's memory. 

To preserve this characteristic, lists must always be contiguous; there can't be any "gaps", or else Python couldn't calculate its memory. 
One special thing about Python and Lists is that it actually assigns more memory that required for a list. For example, a list of 4 elements may have enough space to hold 8. This is helpful because it allows us to simply add an item to the end of the list, as in other languages where this doesn't occur, the language must assign a larger chunk of memory if the initial size is exceeded, taking more time. 
The net effect is that it's much faster to add and remove items at the end of a list them at the front! Adding at the end usually only requires expanding into the new memory space, occasionally you'll run out of space, but it isn't as time consuming as the alternative.
Now the reason our earlier stack example had such a fast time with `Stack` is clear.

## Analyzing Algorithm Running Time
In evaluation the quality of our programs, we have discussed two metrics so far, the correctness (does code work) / does it handle errors appropriately, the second is the design of the code, including documentation and readability for both client and user application.

From what we've seen about the two different stacks, we also have **Running Time Efficiency** to consider as a quality of the program. 

## Observations about Runtime
First, recall that for most algorithms, their running time depends on the size of the input - as the input number or list gets larger, we expecting the algorithms operating time to increase as well. 
When we measure efficiency, we really care about a function of the amount of time it takes to run in terms of the size of the input. We can ride something like *T(n)* to denote the runtime of a function of size *n* (note that it doesn't have to be *n*).

We can measure runtime with tools like `timeit` but there are so many factors that can influence the runtime, like how powerful your machine is. As with Schrödinger’s cat, even the act of observing the runtime can affect performance!

This is where we employ **Big-Oh** notation. Which allows form an elegant way to characterize the *type* of growth of the runtime of an algorithm. 
Lets look at the below example, note that **Big-Oh** has general terms of _linear_, _quadratic_, or _logarithmic_ growth.
```python
for item in lst:
    # do something with item
```
This above snippet of code would have a runtime of *O(n)* where n is the length of the list. This is a linear runtime where the runtime grows linearly with the size of the list.

### Ignoring Constants, Focusing on Behaviour as Set Grows
In CSC236 we'll learn more about the formal mathematical definition of **Big-Oh** notation, but this isn't covered here. At it's core, the notation allows us to analyze the running time of algorithms while ignoring two things.
1. The constants and lower-order terms involved in step counting
2. The algorithm's running time on small input, the key idea of this testing is to see it in large data sets. 

Noen that these points mean that **Big-Oh** notation is not necessarily suitable for all purposes, for example, mergesort runs in time O(n log(n)), and insertion sort runs in time O(n^2). In small datasets, insertion sort is faster, but usually mergesort is faster. 

## Terminology and Mathematical Intuition
Here is a table that summerizes the more common **Big-Oh** classes.
![[Pasted image 20250123163900.png]]
Notice that exponential growth is written is 2^n and not n^2.

## Constant Time
There is one more use for **Big-Oh** notation that we require, and thats functions with *asymptotic* growth that doesn't depend on the input. For example, consider the function f(n) = 10, which is constant. This would have a O(1) running time. 