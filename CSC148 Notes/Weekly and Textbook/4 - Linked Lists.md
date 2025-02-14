---
tags:
  - csc148
---

# Chapter 4.1 - Introduction to Linked Lists
We've seen that Python lists are an array-based implementation of the list ADT, and that they have some *drawbacks*.
Inserting and deleting items in a list can require shifting many elements in the program's memory. For example, we saw how inserting and deleting at the *front* of a built-in list takes time proportional to the length of the list, since every item needs to be shifted. 

Now, we will study a completely different implementation of the List ADT, which will attempt to address the efficiency short coming. 
To do so, we'll use a new data structure called the **linked list**. Our goal is to create a new Python class that behaves exactly like the built-in `list` class. 
```python
for i in range(n):
	nums.append(i)
print(nums)
```
This code should work with our **linked list** just like how it would work for a common list.

## The Concept of "links"
The reason why a Python `list` often requires elements to be shifted back and forth is because the elements of a Python `list` is stored in *contiguous slots* in memory. What if we didn't attempt to have this contiguity? 
If we had a variable referring to the first element of a list, how would we know where the rest of the elements were? We can resolve this easily. If we store along with each element a reference to the next element in the list.

This bundling of data - an element plus a reference to the next element - should suggest something familar... We need a *new* class whose instance attributes are exactly these pieces of data. We'll call this class a *node*, and implement it in Pytthon as follows:
```python
class _Node:
    """A node in a linked list.

    Note that this is considered a "private class", one which is only meant
    to be used in this module by the LinkedList class, but not by client code.

    === Attributes ===
    item:
        The data stored in this node.
    next:
        The next node in the list, or None if there are no more nodes.
    """
    item: Any
    next: Optional[_Node]

    def __init__(self, item: Any) -> None:
        """Initialize a new node storing <item>, with no next node.
        """
        self.item = item
        self.next = None  # Initially pointing to nothing
```
An instance of `_Node` represents a *single element* of a list; to represent a list of *n* elements. We need n `_Node` instances. The references in all of their `next` attributes link the nodes together into a sequence, even though they are not stored in consecutive locations in memory, and of course this is where linked lists get their name.

## A `LinkedList` Class
The second class we'll use in our implementation is a `LinkedList` class, which will represent the list itself. This class is the one we want client code to use, and in it we'll implement methods that obey the same interface as the built-in `list` class. 

Our first version of the class has a very primitive initializer that always creates an empty list.
```python
class LinkedList:
    """A linked list implementation of the List ADT.
    """
    # === Private Attributes ===
    # The first node in this linked list, or None if this list is empty.
    _first: Optional[_Node]

    def __init__(self) -> None:
        """Initialize an empty linked list.
        """
        self._first = None
```

### Example: Building Links
Of course, in order to do anything interesting with linked lists, we need to be able to create arbitrarily long linked lists! We'll see more sophisticated ways of doing this later, but for practice we'll violate privacy concerns and just **manipulate** the private attributes direction.
```python
>>> linky = LinkedList()  # linky is empty
>>> print(linky._first)
None
>>> node1 = _Node(10)   # New node with item 10
>>> node2 = _Node(20)   # New node with item 20
>>> node3 = _Node(30)   # New node with item 30
>>> print(node1.item)
10
>>> print(node1.next)   # New nodes don't have any links
None
>>> node1.next = node2  # Let's set some links
>>> node2.next = node3
>>> node1.next is node2 # Now node1 refers to node2!
True
>>> print(node1.next)
<_Node object at 0x000000000322D5F8>
>>> print(node1.next.item)
20
>>> print(node1.next.next.item)
30
>>> linky._first = node1   # Finally, set linky's first node to node1
>>> linky._first.item      # linky now represents the list [10, 20, 30]
10
>>> linky._first.next.item
20
>>> linky._first.next.next.item
30
```
The most common mistake students make when first starting out with linked lists is confusing an individual node object with the item it stores. So in the example above, there's a big difference between `node1` and `node1.item`: the former is a `_Node` object containing the number 10, while the latter *is* the number 10 itself

As you start writing code with linked lists, you'll sometimes want to operate on nodes, and sometimes want t operate on items. Making sure you always know exactly which type you're working with is vital. 

## Linked List Diagrams
Because each element of a linked list is wrapped in a `_Node` object, complete memory model diagrams of linked lists are quite a bit larger than those corresponding to Python's array based lists. For example, the following is a diagram showing a linked list named `linky` with four elements, `109, 68, 71, 3`.![[Pasted image 20250131222815.png]]
While memory model diagrams are a useful tool, they can be overkill if you want a quick and dirty list diagram. So below we have a stripped down version which removes all of the "boilerplate" types and attribute names. 
The first one keeps the "item" references as arrows to seperate memory objects, while the second goes a step further in simplification by writing the numbers directly in the node boxes.![[Pasted image 20250131223111.png]]
Note: this looks so much better. 😭

Please note that the `append` method is something we'll look into during this chapter, this section is just an intro. 

# Chapter 4.2 - Traversing Linked Lists
The final example in the previous section ended with the sequence of expressions `linky._first.item`, `linky._first.next.item`, and `linky._first.next.next.item` to access the linked list’s first, second, and third elements, respectively.

This is, of course, ugly and cumbersome way of accessing list elements. In this section we'll study how to *traverse* a linked list one at a time, regardless of how long the linked list actually is.
Linked list code is one of the most technically challenging and important parts of this course, so lock in!

Before we write code to traverse a linked list, let's remember how traversal might look for a built-in list, manually using an index variable `i` to keep track of where we are in the list. 
```python
i = 0
while i < len(my_list):
    ... do something with my_list[i] ...
    i = i + 1
```
This code segment consists of four parts:

1. Initialize the index variable `i` (0 refers to the start of the list).
2. Check if we’ve reached the end of the list.
3. Do something with the current element `my_list[i]`.
4. Increment the index.

This method takes advantage of the fact that Python already gives us a way to access elements of a built-in list by index (using square brackets). In a linked list, we don't have this luxury, and so the major difference to this pattern is that we now keep a variable that refers to which `_Node` object we're on in the loop.
Traversing a linked list consist of the exact same steps, except that the temporary variable now refers to a particular `_Node` object rather than an index. Other than this change, its the same!
```python
curr = my_linked_list._first   # 1. Initialize curr to the start of the list
while curr is not None:        # 2. curr is None if we've reached the end of the list.
    ... curr.item ...          # 3. Do something with the current *element* curr.item.
    curr = curr.next           # 4. "Increment" curr, setting it to refer to the next node.
```
For example, here is a `LinkedList` method that prints out every item in a linked list.
```python
class LinkedList:
    def print_items(self) -> None:
        """Print out each item in this linked list."""
        curr = self._first
        while curr is not None:
            print(curr.item)
            curr = curr.next
```

And here is a `LinkedList` method that's a bit more complicated but uses the same traversal template. The goal of this method is to convert a linked list into a build-in Python list, in a non-mutating fashion. (return a Python list without changing the original list)
```python
def to_list(self) -> list:
        """Return a (built-in) list that contains the same elements as this list.
        """
        items = []
        curr = self._first
        while curr is not None:
            items.append(curr.item)
            curr = curr.next

        return items
```
Note: that is beautiful.

## Our Philosophy for "code templates"
You may be surprised about our presentation of a code template for traversing a linked list. After all, templates aren't bad, but we shouldn't copy-paste... Right?

In fact, over the next few weeks of this course we'll *encourage* you to use certain code templates to help get started writing and organizing your code. 
Bottom of the line, we use code templates to get *started* writing code.

Below, this is always the template we should use to iterate through a linked list.
```python
curr = my_linked_list._first
while curr is not None:
    ... curr.item ...
    curr = curr.next
```
The ellipsis (...) are the area you should modify to suit your needs.

# Chapter 4.3 - Linked List Mutation
All of the linked list methods we have looked at so far are *non-mutating*, meaning they did not change the linked list upon which they were called. Here's a brief reminder of how to traverse via `while` loop.
```python
# self is a LinkedList
curr = self._first
while curr is not None:
    ... curr.item ...
    curr = curr.next
```

We started with these methods because they're generally easier to understand than their mutating cousins. Now, we're going to look at the two major **mutating** operations on linked lists. First, we want it to look like the built-in list mutation.
```python
>>> lst = []
>>> lst.append(1)
>>> lst.append(2)
>>> lst.append(3)
>>> lst
[1, 2, 3]

# Built in vs LinkedList version

class LinkedList:
    def append(self, item: Any) -> None:
        """Add the given item to the end of this linked list."""
```

Recall that a `LinkedList` object has only one attribute, a reference to the `first` node in the list. Unfortunately, this means that we have a lot of work to do to implement our `append` function for the linked list. Let's begin with a basic code template!
```python
def append(self, item: Any) -> None:
    """Add the given item to the end of this linked list."""
    curr = self._first
    while curr is not None:
        ... curr.item ...
        curr = curr.next
```
This template is a good start, but now our thinking just begin. 
First: what do we do with `curr.item`? The answer is "Nothing!" - we don't need to actually use any of the existing items in the list, and instead are just going through the list to get to the last node.
Unfortunately, there's a problem with the loop: this loop is designed to keep going until we've processed everything, where our `curr` becomes `None`. This is going to fair for our purposes: we want to stop the loop as soon as we reach the last node. We modify our loop condition to check whether the current node is the last one by using `curr.next` is `None` instead.
```python
def append(self, item: Any) -> None:
        """Add the given item to the end of this linked list."""
        curr = self._first
        while curr.next is not None:
            curr = curr.next

        # After the loop, curr is the last node in the LinkedList.
        # assert curr is not None and curr.next is None
```
At this point, the astute reader will point out a flaw in this change: we aren't  guranteed that `curr` starts off as a node - it could be `None`. But because we don't want to deal with that, we just say `TODO: what if curr starts off as None?`

So then after the loop ends, we know that `curr` refers to the last node in the linked list, and we are finally in a position to add the given item to the linked list. To do so, we need to create a new node and then connect it in.

And finally, let's handle that TODO. We know that the documentation of our `LinkedList` class that `self._first` can be `None` if `self` refers to an empty linked list. But in this case, all we need to do is add the new item to be the first item in the linked list.
```python
def append(self, item: Any) -> None:
    """Add the given item to the end of this linked list."""
    curr = self._first
    if curr is None:
        new_node = _Node(item)
        self._first = new_node
    else:
        while curr.next is not None:
            curr = curr.next

        # After the loop, curr is the last node in the LinkedList.
        # assert curr is not None and curr.next is None
        new_node = _Node(item)
        curr.next = new_node
```

### Example: A more General Initializer
With our `append` method in place, we can now stop creating linked lists manually, and modify our initializer for `LinkedList` to take in a list of values which we'll then append one at a time. 
```python
class LinkedList:
    def __init__(self, items: list) -> None:
        """Initialize a new linked list containing the given items.

        The first node in the linked list contains the first item
        in <items>.
        """
        self._first = None
        for item in items:
            self.append(item)
```
While correct, this code is rather inefficient, if it were me, to fix it attempt to create an extend() method. Not sure if this is right though.

## Index-Based Insertion
Now suppose we want to implement a more general form of insertion that allows the user to specify the index of the list to insert a new item into. (basically the built-in list.insert() method)
```python
class LinkedList:
    def insert(self, index: int, item: Any) -> None:
        """Insert a new node containing item at position <index>.

        Precondition: index >= 0.

        Raise IndexError if index > len(self).

        Note: if index == len(self), this method adds the item to the end
        of the linked list, which is the same as LinkedList.append.

        >>> lst = LinkedList([1, 2, 10, 200])
        >>> lst.insert(2, 300)
        >>> str(lst)
        '[1 -> 2 -> 300 -> 10 -> 200]'
        >>> lst.insert(5, -1)
        >>> str(lst)
        '[1 -> 2 -> 300 -> 10 -> 200 -> -1]'
        """
```
As with append, our first step is to traverse the list until we reach the correct index; and if we want the node to be inserted into position `index`, we need access the mode at position `(index-1)`, and to modify our code template to store not just the current node, but the current index of that code as well. 
```python
def insert(self, index: int, item: Any) -> None:
    curr = self._first
    curr_index = 0

    while curr is not None and curr_index < index - 1:
        curr = curr.next
        curr_index += 1
```

This loop condition is pretty complicated, so it's worth spending some time to unpack. Here, we're saying that the loop should keep going when the current node is not `None` *and* when the current index is less than our target index `(index - 1)`. 
This means that when the loop is over, the current node is `None` *or* the current index has reached the target index. Or it could be both, thus our code must handle each separately.
```python
def insert(self, index: int, item: Any) -> None:
	curr = self._first
	curr_index = 0

	while curr is not None and curr_index < index - 1:
		curr = curr.next
		curr_index += 1

	# assert curr is None or curr_index == index - 1
	if curr is None:
		pass
	else: # curr_index == index - 1
		pass
```
Now if `curr` is `None` then the list doesn't have a node at position `index - 1`, and so that index is out of bounds. In this case, we should raise an `IndexError`.
On the other hand, if `curr` is not `None`, then we've reached the desired index, and can insert the new code using the same strategy as `append`.
```python
def insert(self, index: int, item: Any) -> None:
	curr = self._first
	curr_index = 0

	while curr is not None and curr_index < index - 1:
		curr = curr.next
		curr_index += 1

	# assert curr is None or curr_index == index - 1
	if curr is None:
		# index - 1 is out of bounds. The item cannot be inserted.
		raise IndexError
	else: # curr_index == index - 1
		# index - 1 is in bounds. Insert the new item.
		new_node = _Node(item)
		curr.next = new_node  # Hmm...
```
Well, the problem now is that with the last `else` branch, unlike `append`, `curr` might have had other nodes after it! Simply setting `curr.next = new_node` loses the reference to the old node at position `index`, and any subsequent nodes after that one. So before overwriting `curr.next`, we need to update `new_node` so that it refers to the old node at position `index`:
```python
def insert(self, index: int, item: object) -> None:
	curr = self._first
	curr_index = 0

	while curr is not None and curr_index < index - 1:
		curr = curr.next
		curr_index += 1

	# assert curr is None or curr_index == index - 1
	if curr is None:
		# index - 1 is out of bounds. The item cannot be inserted.
		raise IndexError
	else: # curr_index == index - 1
		# index - 1 is in bounds. Insert the new item.
		new_node = _Node(item)
		new_node.next = curr.next  # THIS LINE IS IMPORTANT! (changes the id)
		curr.next = new_node
```

### Warning! Common Error Ahead (with solution)
When writing mutating methods on linked lists, we very often update the links of individual nodes in the list. We must be very careful when doing so, because the order in which we update the links really matters, and often only one order results in the correct behaviour.
For example, this order of link updates in the final `else` branch doesn’t work:
```python
curr.next = new_node
new_node.next = curr.next
```
On the second line, `curr.next` has already been updated, and its old value has already been lost. the second line is now equivalent to writing `new_node.next = new_node`, which is certainly not what we want!

The reason this type of error is so insidious is that the code *looks* very similar to the correct code, and so you can only detect it by carefully tracing through the updates line-by-line.

To mitigate this problem, we'll take advantage of a pretty nice Python feature known as *multiple* (or *simultaneous*) assignment
```python
a, b = 1, 2  # Assigns 1 to a and 2 to b
```
The beauty of this approach is that the expressions on the right side are *all* evaluated before any new values are assigned, meaning that you don't need to worry about the order in which you write them. For example, these two assignment statements are equivalent.
```python
# Version 1
curr.next, new_node.next = new_node, curr.next
# Version 2
new_node.next, curr.next = curr.next, new_node
```

### Tidying up: Don't Forget About Corner Cases!
Our `insert` implementation has one problem: what if `index = 0`? In this case, it doesn't make sense to iterate to the `(index - 1)`-th node! This is another special case which we need to handle seperately by modifying `self._first`
```python
 def insert(self, index: int, item: Any) -> None:
	if index == 0:
		new_node = _Node(item)
		self._first, new_node.next = new_node, self._first
	else:
		curr = self._first
		curr_index = 0

		while curr is not None and curr_index < index - 1:
			curr = curr.next
			curr_index += 1

		# assert curr is None or curr_index == index - 1
		if curr is None:
			# index - 1 is out of bounds. The item cannot be inserted.
			raise IndexError
		else: # curr_index == index - 1
			# index - 1 is in bounds. Insert the new item.
			new_node = _Node(item)
			curr.next, new_node.next = new_node, curr.next
```

### Exercise: Index-Based Deletion
```python
def pop(self, index: int) -> Any:
	"""Remove and return node at position <index>.

	Precondition: index >= 0.

	Raise IndexError if index >= len(self).

>>> lst = LinkedList([1, 2, 10, 200])
>>> lst.pop(2)
	10
>>> lst.pop(0)
	1
	"""
	# Warning: the following is pseudo-code, not valid Python code!

	# 1. If the list is empty, you know for sure that index is out of bounds...
	# 2. Else if index is 0, remove the first node and return its item.
	# 3. Else iterate to the (index-1)-th node and update links to remove
	#    the node at position index. But don't forget to return the item!
```

Now I have to make it... Good luck!
```python

def pop(self):
	"""Remove and return the last item in the linked list."""
	if not self._first:  # Empty list case
		raise IndexError("pop from empty linked list")

	# If there's only one element
	if self._first == self._last:
		value = self._first.item
		self._first = None
		self._last = None
		return value

	# Traverse to find the second-last node
	current = self._first
	while current.next != self._last:
		current = current.next

	value = self._last.item
	self._last = current
	self._last.next = None
	return value
```

# Chapter 4.4 - Linked Lists and Running Time
To wrap up the discussion of linked lists, we return to our original motivation of studying linked lists: improving the efficiency of some of the basic list operations.

We've already discussed the running time of three operations of array-based lists:

- Looking up an element of the list by its index (e.g., `lst[i]`) takes _constant time_, i.e., is independent of the length of the list, or even which index we’re looking up. In the language of Big-Oh notation, we write _O(1)_ to represent this time.
- Inserting or removing an element at index _i_ (_0 ≤ i < n_) in a list of length _n_ takes time proportional to _n - i_, which is the number of list elements that need to be shifted when this operation occurs. Remember that Big-Oh notation is used to describe “proportional to” relationships, and so we write that this operation takes time _O(n - i)_.

In particular, if we only consider inserting/removing at the _front_ of an array-based list (so _i = 0_), this takes time linear in the length of the list: _O(n)_. On the other hand, if we only consider inserting/removing at the _end_ of such a list (_i = n_), this is a constant time operation: _O(1)_.

## Turning to Linked Lists
What about the corresponding operations for `LinkedList`? Let's study our code for `LinkedList.insert`, first looking at the special cases of inserting into the front and end of a linked list.
```python
def insert(self, index: int, item: Any) -> None:
    # Create a new node
    new_node = _Node(item)

    # Need to do something special if we insert into the first position.
    # In this case, self._first *must* be updated.
    if index == 0:
        new_node.next = self._first
        self._first = new_node
    else:
        # Get the node at position (index - 1)
        curr_index = 0
        curr = self._first
        while curr is not None and curr_index < index - 1:
            curr = curr.next
            curr_index = curr_index + 1

        if curr is None:
            raise IndexError
        else:
            # At this point, curr refers to the node at position (index - 1)
            curr.next, new_node.next = new_node, curr.next
```

We insert into the front of the linked list by calling `insert` with an index argument of 0. In this case, the `if` branch executes, which takes constant time - both assignment statements do not depend of the length of the list. 

On the other hand, suppose we want to insert a item at the end of a linked list, and there's at least one element already in the linked list. The `else` branch executes, and the loop must iterate until it reaches the end of the list, which takes time linear in the length of the list. 

In other words, linked lists have the exact opposite running times as array-based lists for these two operations! Inserting into the `front` of a linked list takes *O(1)* running time, and inserting to the back takes *O(n)* time. Where n is the length of the list.

This may seem disappointing, because not it isn't clear which is "better". In fact, this is pretty typical of computer science: when creating multiple implementations on the public interface, usually its good at some operations but worse at others. It is up to the client to decide what they want. 

## Investigating the Subtleties of "input size"
Despite our discussion above, we haven't yet finished the analysis of linked lists `insert`. We've really only looked at two of the special cases. What about every other case?

The *very first* thing we need to look at the running of each individual line of code. In this case, each individual line, like `curr = self._first`, takes *constant time*. This means that the overall running time depends on the number of lines that execute, and this in turn depends on how many times the loop is run. 
```python
curr_index = 0
curr = self._first
while curr is not None and curr_index < index - 1:
    curr = curr.next
    curr_index = curr_index + 1
```

So how many times does the loop run? These two possibilies for when it stops: when `curr is None` r when `curr_index == index - 1`
- The first case means that the end of the list was reached, which happens after `n` iterations, where `n` is the length of the list (each iteration, the `curr` variable advances by one node).
- The second case means that the loop ran `index - 1` times, since `curr_index` starts at 0 and increases by 1 per iteration.

Since the loop stops when one of the conditions is false, the number of iterations is the *minimum* of these two possibilities: *min(n, index - 1)*

Since the total number of steps is proportional to the number of loop iterations, we can conclude that the asymptotic running time of this method is: _O(min(n, index - 1))_. Where *n* is the size of `self`. But because Big-Oh notation is to simplify our running time expressions by dropping smaller terms, we can drop the -1 and simply write the running time as 
_O(min(n, index)).

## Special Cases

The Big-Oh expression _O(min(n, index))_ for `LinkedList.insert` is the most general expression we could give, since we didn't make any assumptions about the relationship between the length of the linked list and `index`.

Now suppose we assume that `index <= n`, which is plausible since any larger value would raise an `IndexError`. In this case, the Big-Oh expression simplifies to *O(index)*, revealing that under this assumption, the running time depends only on `index` and not on the length of the linked list at all. 

This is pretty subtle so let's say this again. We've got a relationship between the running time of `insert` and the sizes of two of its input. But we can simplify this expression by talking about the relationship between those two input sizes.
Essentially, we say that *if* we treat `index` as small with respect to the size of the list, then the running time of the algorithm doesn't depend on the size of the list. 

On the other hand, suppose `index` is greater than the size of the list; in this case, the Big-Oh expression simplifies to *O(n)*: even though we know this value raises an `IndexError`, our current implementation requires traversing the entire linked list before realizing that `index` is out of bounds!

