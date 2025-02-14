---
tags:
  - csc148
---

# Chapter 5.1 - Motivation: Adding Up Numbers
This week, we're going to learn about a powerful technique called **recursion**, which we'll be using in various ways for the rest of the course. However, recursion is much more than just a programming technique, it is a way of *thinking* about solving problems. This new way of thinking can be summarized in this general strategy:
Identify how an object or problem can be broken down into *smaller instances* with the *same structure*

Let's begin with a series of problems that will demonstrate the need for recursion.
## Summing lists and nested lists

Consider the problem of computing the sum of a list of numbers. Easy enough:

```python
def sum_list(lst: list[int]) -> int:
    """Return the sum of the items in a list of numbers.

    >>> sum_list([1, 2, 3])
    6
    """
    s = 0
    for num in lst:
        s += num
    return s
```
But what if we make the input structure a bit more complex: a list of lists of numbers? After a bit of though, we might arrive at using nested loops to process individual items in the nested list:
```python
def sum_list2(lst: list[list[int]]) -> int:
    """Return the sum of the items in a list of lists of numbers.

    >>> sum_list2([[1], [10, 20], [1, 2, 3]])
    37
    """
    s = 0
    for list_of_nums in lst:
        for num in list_of_nums:
            s += num
    return s
```
Now what happens if we want yet another layer? Computing the sum of items in a list of lists of lists of numbers. Like a nested nested list.
```python
def sum_list3(lst: list[list[list[int]]]) -> int:
    """Return the sum of the items in a list of lists of lists of numbers.

    >>> sum_list3([[[1], [10, 20], [1, 2, 3]], [[2, 3], [4, 5]]])
    51
    """
    s = 0
    for list_of_lists_of_nums in lst:
        for list_of_nums in list_of_lists_of_nums:
            for num in list_of_nums:
                s += num
    return s
```
You see where this is going, ugly code, every time we want to add a list, we add a new layer to the `for` loop. 
### Simplifying Using Helpers
You might have noticed the duplicate code above: in fact, we can use `sum_list` as a helper for `sum_list2` as a helper for `sum_list3`:
```python
def sum_list(lst: list[int]) -> int:
    """Return the sum of the items in a list of numbers.
    """
    s = 0
    for num in lst:
        # num is an int
        s += num
    return s

def sum_list2(lst: list[list[int]]) -> int:
    """Return the sum of the items in a list of lists of numbers.
    """
    s = 0
    for list_of_nums in lst:
        # list_of_nums is a list[int]
        s += sum_list(list_of_nums)
    return s

def sum_list3(lst: list[list[list[int]]]) -> int:
    """Return the sum of the items in a list of lists of lists of         numbers.
    """
    s = 0
    for list_of_lists_of_nums in lst:
        # list_of_lists_of_nums is a list[list[int]]
        s+ = sum_list2(list_of_lists_of_nums)
    return s
```

While this is a nice simplification, it doesn't generalize nicely. If we wanted to implement `sum_list10` we'd need a function that works on lists with ten levels of nesting. Which would be quite rough in this format, thats why we should use recursion. 
Another issue, what if the list contains various levels of lists? Like this,
```python
[[1, [2]], [[[3]]], 4, [[5, 6], [[[7]]]]]
```
No method we implement would work on this. 

# Chapter 5.2 - Nested Lists: A Recursive Data Structure
In the previous example from Chapter 5.1, we showed the limits of a `sum_listX` function, which becomes impossible when various list levels mix. 

In this section, we'll overcome this limitation with a new strategy: breaking the object down into *smaller instances with the same structure as the original*. 

To make this more concrete, let's first identify how the object we are working with, a nested list, can be broken down into smaller instances with the same structure. We will define a new structure that generalize the idea of "list of lists of lists of ..." We define a **nested list** as one of the two types of values:
- A single integer.
- A list of other nested lists (`[lst_1, lst_2, ..., lst_n]`). Each `sublist` is called a _sub-nested-list_ of the outer list.

This is a **recursive definition**: Defining nested lists in terms of other nested lists. 
It may seem a bit odd that we include "single integers" as nested lists; after all, an integer isn't a list in Python. Well actually, it is very convenient to include this part of our recursive definition, and makes both the rest of the definition and the subsequent code we'll write will be far more elegant. 
The _depth_ of a nested list is the maximum number of times a list is nested inside other lists, and we will define the depth of a single integer to be 0. So `[1, 2, 3]` has depth 1, and
```python
[1, [2, [3, 4], 5], [6, 7], 8]
```
has depth 3.

## Summing Up a Nested List
We can use this definition to guide the design of a function that computes the sum on a nested list of numbers:
```python
def sum_nested(obj: Union[int, list]) -> int:
    """Return the sum of the numbers in a nested list <obj>.
    """
    if isinstance(obj, int):
        # obj is an integer
        return obj
    else:
        # obj is a list of nested lists: [lst_1, ..., lst_n]
        s = 0
        for sublist in obj:
            # each sublist is a nested list
            s += sum_nested(sublist)
        return s
```
This is our first example of a *recursive function*: a function that calls itself in its own body. Just as we defined a recursive data structure - nested lists - we have now defined a recursive function that operates on nested lists. Notice how the structure of the data informs the structure of the code: just as the definition of nested lists separates integers and lists of nested lists into two cases, so too does the function `sum_nested`. Thus, the code keeps looping itself until it binds `sublist` to each inner nested list one at a time, calling `sum_nested` on it to compute the sum.

We call the case where `obj` is an integer the **base case** of the code: implementing the function's behaviour on this type of input should be very straightforward, not involving any recursion.
In the other case, where `obj` is a list, this is called a **recursive case**: solving the problem requires decomposing the input into smaller nested lists by calling `sum_nested` on these individually to solve the problem. This is the simplest type of recursive function, it will get more complex later. 

## Partial Tracing: Reasoning About Recursive Calls
We say that the call to `sum_nested` inside the `for` loop is a **recursive function call**, since it appears in the body of `sum_nested` itself. Such function calls are handled in the same way as all other function calls in Python, but the nature of recursion makes that a single initial function call often resulting in many different calls to the exact same function. 
When given a function call on a very complex nested list argument, beginners often attempt to trace through the code carefully, often thinking "Well, we enter the loop and make a recursive call. That recursive call will make this other recursive call, which will make this other recursive call, and so on.” This type of literal tracing is what a computer does, but it's also *extremely time-consuming and error-prone* for humans to do. 
Instead, whenever we are given a recursive function, we use **partial tracing** which has two cases.

### Case 1: The Input Corresponds to a Base Case
For example, suppose we want to trace the call `sum_nested(5)`. The input `5` is an integer, the simplest form of nested list. In our recursive function, this corresponds to the `if` condition being true, and so to trace our code we can simply trace the `if` branch directly, *completely ignoring the `else` branch*. 
```python
def sum_nested(obj: Union[int, list]) -> int:
    """Return the sum of the numbers in a nested list <obj>.
    """
    if isinstance(obj, int):
        # obj is an integer
        return obj
    else:
        ...
```
Tracing this is simple, as the 5 is immediately returned using the `return obj`, since the sum of a single integer is just the integer itself. 

### Case 2: The input corresponds to a recursive case

For example, suppose we want to trace the call `sum_nested([1, [2, [3, 4], 5], [6, 7], 8])` and verify that the output is the correct value of 36. This input corresponds to the `else` branch of `sum_nested`, which we show below:

```python
    else:
        # obj is a list of nested lists: [lst_1, ..., lst_n]
        s = 0
        for sublist in obj:
            # each sublist is a nested list
            s += sum_nested(sublist)
        return s
```

This code is an instance of the _accumulator_ pattern, in which we loop over `obj`, and for each value update the accumulator variable `s`. Naive tracing is challenging, though, because at each loop iteration we’ll make a recursive call to `sum_nested`, which may result in tracing many subsequent recursive calls until we finally can update our accumulator.

The key idea of partial tracing is the following: we'll trace through the above code, but every time there's a recursive call, instead of tracing into it, we *assume it is correct*, and simply use the correct return value and continue tracing the rest of the code. 

To facilitate this, we use a _table of values_, that we build up as follows.

1. First, we take our input `obj`, `[1, [2, [3, 4], 5], [6, 7], 8]`, and identify each sub-nested-list. Note that there are only four of them (we don’t count sub-nested-lists of sub-nested-lists).
		1
		[2, [3, 4], 5]
		[6, 7]
		8
2. Next, beside each one we write down what `sum_nested` *should* return on each input. Remember that we aren't doing any tracing here; instead, we're filling this in based on the *documentation* for `sum_nested`.
		1
		1
		[2, [3, 4], 5]
		14
		[6, 7]
		13
		8
		8
3. Finally, we trace through the code from the original `else` block, updating the value of the accumulator's using the above table, we show these updates in tabular form below. 
		1
		1
		1 (s += 1)
		[2, [3, 4], 5]
		14
		15 (s += 14)
		[6, 7]
		13
		28 (s += 13)
		8
		8
		36 (s += 8)

From our above table, we see that after the loop completes, the final value of `s` is `36` and this value is returned by our original call to `sum_nested`. It also happens to be the correct value! (meaning that's what the test gives as well)

## Why Does Partial Tracing Work?
When students first see the technique, they are suspicious and think that "why can we just *assume* the recursive calls are correct???" "What if they aren't?"
It turns out this assumption is valid as long as both of the following properties hold:
1. You are sure that your base case is correct. (You can usually fully trace the code directly to determine this.)
2. Every time you make a recursive call, it is on a _smaller_ input than the original input.

Here's how to apply these ideas to show that a `sum_nested` is correct.
1. Since I’m confident in the base case for `sum_nested`, I now know that `sum_nested` is correct for all nested lists of depth 0.
2. If I have a nested list of depth 1, every recursive call I make is going to be on a nested list of depth 0. I’m going to assume that they’re correct (because of my previous statement). So then using partial tracing, I’m confident that `sum_nested` is correct for all nested lists of depth 1.
3. If I have a nested list of depth 2, every recursive call I make is going to be on a nested list of depth 0 or 1. I’m going to assume that they’re correct (because of my previous two statements). So then using partial tracing, I’m confident that `sum_nested` is correct for all nested lists of depth 2.
4. And so on.

This idea is formalized in the *Principle of Mathematical Induction*, a formal proof technique that is expanded on in CSC165/240. It's beyond the scope of this course, but rest assured that it tells us that partial tracing is a valid technique for reasoning about the correctness of recursive functions.

### What This Means for Testing and Debugging
The flip side of this is, if you have a recursive function and it’s _incorrect_ (say, failing a test case that you wrote), there can only be one of the following problems:

1. A base case is incorrect.
2. One or more of the recursive calls is _not_ being made on a smaller input.
3. The recursive case is incorrect, _even if you assume_ that every recursive call is correct! In other words, the problem isn’t in the recursive call itself, it’s in the code surrounding that recursive call.

## Design Recipe for Recursive Functions
1. Identify the recursive structure of the problem, which can usually be reduced to finding the recursive structure of the _input_. Figure out if it’s a nested list, or some other data type that can be expressed recursively.
    
    Once you do this, you can often write down a _code template_ to guide the structure of your code. For example, the code template for nested lists is:
    
    ```python
    def f(obj: Union[int, list]) -> ...:
        if isinstance(obj, int):
            ...
        else:
            for sublist in obj:
                ... f(sublist) ...
    ```
    
2. Identify and implement code for the **base case(s)**. Note that you can usually tell exactly what the base cases are based on the structure of the input to the function. For nested lists, the common base case is when the input is an integer—and if you follow the above template, you won’t forget it.
    
3. Write down a concrete example of the function call on an input of some complexity (e.g., a nested list of depth 3). Then write down the relevant recursive function calls (determined by the structure of the input), and what they output **based on the docstring of the function**. In other words, write down the first two columns of the table we described above in the section on partial tracing.
    
4. Take your results from step 3, and figure out how to combine them to produce the correct output for the original call. This is usually the hardest step, but once you figure this out, you can implement the recursive step in your code!