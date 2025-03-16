# 6.1 - Introduction to Trees
While the List abstract data type is extremely common and useful, not all data has a natural linear order. Family trees, corporate organization charts, classification schemes, and even file storage on computers all follow a *hierarchical structure*, in which each entity is linked to multiple entities "below" it
In Computer Science, we use a **tree** data structure to represent this type of data. Trees are a **recursive** data stricture, with the following definition: A tree is either
- empty, or
- Has a **root value** connected to any number of other trees, called the **subtrees** of the tree.

We generally draw the root at the top of the tree; the rest of the tree consists of subtrees that are attached to the root. Note that a tree can contain a root value but not have any subtrees: this occurs in a tree that contains a single item.
## Tree Terminology
![[Pasted image 20250227185713.png]]
A tree is either **empty** or **non-empty**. Every non-empty tree has a **root**, which is connected to zero or more **subtrees.** The root value of the above tree is labeled A; it is connected to three subtrees.
- The **size** of a tree is the number of values in the tree. 
- A **leaf** is a value with no subtrees. The leaves of the above tree are labeled E, F, G, J, and I. 
- The **height** of a tree is the length of the _longest_ path from its root to one of its leaves, counting the number of values on the path. The height of the above tree is 4. 
- The **children** of a value are all values directly connected underneath that value. The children of A are B, C, and D. Note that the number of children of a value is equal to the number of its subtrees, but that these two concepts are quite different. The **descendants** of a value are its children, the children of its children, etc. This can be defined recursively as “the descendants of a value are its children, and the descendants of its children.” 
- Similarly, the **parent** of a tree value is the value immediately above and connected to it; each value in a tree has exactly one parent, except the root, which has no parent. The **ancestors** of a value are its parent, the parent of its parent, etc. This too can be defined recursively: “the ancestors of a value are its parent, and the ancestors of its parent.”
- **Note**: sometimes, it will be convenient to say that descendants/ancestors of a value include the value itself; we’ll make it explicit whether to include the node or not when it comes up. Note that a value is **never** a child of itself, nor a parent of itself.

# 6.2 - A Tree Implementation
Here is a simple implementation of a tree in python 
```python
class Tree:
    """A recursive tree data structure.
    """
    # === Private Attributes ===
    # The item stored at this tree's root, or None if the tree is empty.
    _root: Optional[Any]
    # The list of all subtrees of this tree.
    _subtrees: list[Tree]

    # === Representation Invariants ===
    # - If self._root is None then self._subtrees is an empty list.
    #   This setting of attributes represents an empty tree.
    #
    #   Note: self._subtrees may be empty when self._root is not None.
    #   This setting of attributes represents a tree consisting of just one
    #   node.

    def __init__(self, root: Optional[Any], subtrees: list[Tree]) -> None:
        """Initialize a new tree with the given root value and subtrees.

        If <root> is None, this tree is empty.
        Precondition: if <root> is None, then <subtrees> is empty.
        """
        self._root = root
        self._subtrees = subtrees

    def is_empty(self) -> bool:
        """Return whether this tree is empty.

        >>> t1 = Tree(None, [])
        >>> t1.is_empty()
        True
        >>> t2 = Tree(3, [])
        >>> t2.is_empty()
        False
        """
        return self._root is None
```
Our initializer here always creates either an empty tree (when `root` is `None`), or a tree with a value and the given subtrees.
Note that it is possible for a `root` to be `None`, but `subtrees` to still be empty: this represents a tree with a single root value, and no subtrees. As we'll soon see, the empty tree and single value cases are generally the base cases when writing recursive code that operates on trees.

## Recursion on Trees
There's a reason we keep asking the same question, "What's the relationship between a tree's X and the X of its subtrees?" Understanding the relationship between a tree and its subtrees - that is, its recursive structure. 
Here's an example, showing that the size of a non-empty tree is the sum of the sizes of its subtrees, plus 1 for the root; the size of an empty tree is 0.
```python
def __len__(self) -> int:
    """Return the number of items contained in this tree.

    >>> t1 = Tree(None, [])
    >>> len(t1)
    0
    >>> t2 = Tree(3, [Tree(4, []), Tree(1, [])])
    >>> len(t2)
    3
    """
    if self.is_empty():
        return 0
    else:
        size = 1  # count the root
        for subtree in self._subtrees:
            size += subtree.__len__()  # could also do len(subtree) here
        return size
```
- We can generalize this nicely to a template for recursive methods on trees:
```python
def f(self) -> ...:
    if self.is_empty():
        ...
    else:
        ...
        for subtree in self._subtrees:
            ... subtree.f() ...
        ...
```
- Of course, often the ellipses will contain some reference to `self._root` as well!

### An Explicit size-one Case
Often when first dealing with trees, students like to think explicitly about the case where the tree consist of a single item. We will modify our previous `__len__` implementation to handle this case separately by adding a single check.
```python
def __len__(self):
    if self.is_empty():         # tree is empty
        return 0
    elif self._subtrees == []:  # tree is a single item
        return 1
    else:                       # tree has at least one subtree
        size = 1  # count the root
        for subtree in self._subtrees:
            size += subtree.__len__()
        return size
```
Sometimes, the check will be *necessary*: we'll want to do something different for a tree with a single item than for either an empty tree or one with at least one subtree. And sometimes, this check will be *redundant*, already being handled by a previous recursive step.
In the case of `__len__`, the latter situation applies. The single-item case is already handled during the recursive step, which will simply return 1 when there are no subtrees.
However the possibility of having a redundant cases shouldn't discourage you, as you should always start with including this case.
ex. with this template
```python
def f(self) -> ...:
    if self.is_empty():         # tree is empty
        ...
    elif self._subtrees == []:  # tree is a single value
        ...
    else:                       # tree has at least one subtree
        ...
        for subtree in self._subtrees:
            ... subtree.f() ...
        ...
```

## Traversing A Tree
Because the elements of a list have a natural order, lists are pretty straightforward to traverse, making it easy to write a `__str__` method. With trees, there is a non-linear ordering on the elements, so we have to adjust our `__str__` implementation.
What if we start with the value of the root, then recursively add on the `__str__` for each subtree. The base case is when the tree is empty, and in this case the method return an empty string.
```python
def __str__(self) -> str:
    """Return a string representation of this tree.
    """
    if self.is_empty():
        return ''
    else:
        # We use newlines (\n) to separate the different values.
        s = f'{self._root}\n'
        for subtree in self._subtrees:
            s += str(subtree)  # equivalent to subtree.__str__()
        return s
```
- Consider what happens when we run this on the following tree structure:
```python
>>> t1 = Tree(1, [])
>>> t2 = Tree(2, [])
>>> t3 = Tree(3, [])
>>> t4 = Tree(4, [t1, t2, t3])
>>> t5 = Tree(5, [])
>>> t6 = Tree(6, [t4, t5])
>>> print(t6)
6
4
1
2
3
5
```
We know that 6 is the root of the tree, but it's ambiguous how many children it has. In other words, while the *items* in the tree are correctly included, we lost the *structure* of the tree itself. 
Drawing inspiration from folder structures on computers, we can use indentation to differentiate between the different levels of a tree. 
```python
>>> # (The same t6 as defined above.)
>>> print(t6)
6
  4
    1
    2
    3
  5
```

We want `__str__` to return a string that has 0. indents before the root value. 1 indent before its children, 2 before their children, and so on. We will need the recursive calls to act differently, which is exactly what we want for a recursive structure.
Here is the code for it.
```python
def _str_indented(self, depth: int) -> str:
    """Return an indented string representation of this tree.

    The indentation level is specified by the <depth> parameter.
    """
    if self.is_empty():
        return ''
    else:
        s = '  ' * depth + str(self._root) + '\n'
        for subtree in self._subtrees:
            # Note that the 'depth' argument to the recursive call is
            # modified.
            s += subtree._str_indented(depth + 1)
        return s
```
- Now we can implement `__str__` simply by making a call to `_str_indented`
```python
def __str__(self) -> str:
    """Return a string representation of this tree.
    """
    return self._str_indented(0)

>>> t1 = Tree(1, [])
>>> t2 = Tree(2, [])
>>> t3 = Tree(3, [])
>>> t4 = Tree(4, [t1, t2, t3])
>>> t5 = Tree(5, [])
>>> t6 = Tree(6, [t4, t5])
6
  4
    1
    2
    3
  5
```

## Technical Note: Optional Parameters
One way to customize the behaviour of functions is to make a parameter **optional** by giving it a **default value**. This can be done for any function, recursive or non-recursive, inside or outside a class. The syntax for doing so is quite simple; we use it in this revised version.
```python
def _str_indented(self, depth: int=0) -> str:
    """Return an indented string representation of this tree.

    The indentation level is specified by the <depth> parameter.
    """
    if self.is_empty():
        return ''
    else:
        s = '  ' * depth + str(self._root) + '\n'
        for subtree in self._subtrees:
            # Note that the 'depth' argument to the recursive call is
            # modified.
            s += subtree._str_indented(depth + 1)
        return s
```
In this version of `_str_indented`, `depth` is an optional parameter that can either be included or not included when this method is called. 
So we can call `t._str_indented(5)`, which sets the depth equal to 5, but if we called it as `t._str_indented()`, it would still work, setting depth to equal 0. 
Optional parameters are **VERY HELPFUL** is it allows for more flexible functions and methods for a variety of situations. Keep in mind,
- All optional parameters must appear _after_ all of the required parameters in the function header.
- **Do NOT** use mutable values like lists for your optional parameters. (If you do, the code will appear to work, until it mysteriously doesn’t. Feel free to ask more about this during office hours.) Instead, use optional parameters with immutable values like integers, strings, and `None`.

# 6.3 - Mutating Trees
Now that we have some experience working with trees, let's talk about mutating them. There are two fundamental mutating operations that we want, insertion and deletion. Insertion will be covered separately.
```python
def delete_item(self, item: Any) -> bool:
    """Delete *one* occurrence of <item> from this tree.

    Return True if <item> was deleted, and False otherwise.
    Do not modify this tree if it does not contain <item>.
    """
```
To implement this method, we begin by using our code template. For this case we'll use the three-branch version, which explicitly separate the size-one case.
```python
def delete_item(self, item: Any) -> bool:
    """Delete *one* occurrence of <item> from this tree.

    Return True if <item> was deleted, and False otherwise.
    Do not modify this tree if it does not contain <item>.
    """
    if self.is_empty():
        ...
    elif self._subtrees == []:
        ...
    else:
        ...
        for subtree in self._subtrees:
            ... subtree.delete_item(item) ...
        ...
```
In the recursive step, we're going to first check whether the item is equal to the root; if it is, then we only need to remove the root, otherwise we have to recurse and look through the subtrees.
```python
def delete_item(self, item: Any) -> Bool:
    if self.is_empty():
        return False              # item is not in the tree
    elif self._subtrees == []:
        if self._root != item:    # item is not in the tree
            return False
        else:                     # resulting tree should be empty
            self._root = None
            return True
    else:
        if self._root == item:
            self._delete_root()   # delete the root
            return True
        else:
            for subtree in self._subtrees:
                subtree.delete_item(item)
```
Deleting the root when there are subtrees is far more challenging, so we'll do this later. For now we can use a helpful method called `_delete_root` that doesn't exist yet, it's just a reminder to do it later.
The final `else` branch may seem done as well, it it has serious problems.
1. It doesn’t return anything, violating this method’s type contract.
2. If one of the recursive calls successfully finds and deletes the item, no further subtrees should be modified (or even need to be recursed on).
The solution to both of these problem like in the fact that our loop right now doesn't store the value of recursive calls anywhere. The key insight is that we should use the return value of each recursive call to determine whether an item was deleted, and whether to continue on to the next subtree:
```python
def delete_item(self, item: Any) -> Bool:
    if self.is_empty():
        return False              # item is not in the tree
    elif self._subtrees == []:
        if self._root != item:    # item is not in the tree
            return False
        else:                     # resulting tree should be empty
            self._root = None
            return True
    else:
        if self._root == item:
            self._delete_root()   # delete the root
            return True
        else:
            for subtree in self._subtrees:
                deleted = subtree.delete_item(item)
                if deleted:
                    # One occurrence of the item was deleted, so we're done.
                    return True

            # If we don't return inside the loop, the item is not deleted from
            # any of the subtrees. In this case, the item does not appear
            # in <self>.
            return False
```
There are *many, many* ways of doing this. Here's one where we just pick the right most subtree, and "promote" its root and subtrees by moving them up a level in the tree.
```python
def _delete_root(self) -> None:
    """Remove the root item of this tree.

    Precondition: this tree has at least one subtree.
    """
    # Get the last subtree in this tree.
    chosen_subtree = self._subtrees.pop()

    self._root = chosen_subtree._root
    self._subtrees.extend(chosen_subtree._subtrees)
```
This may be very satisfying, because while the result certainly is still a tree, it feels like we've changed a lot of the structure of the original tree. I should try making this myself later #148practice

## The Problem of Empty Trees
We're not quite done. 
In our current implementation of `delete_item`, suppose we delete an item that is a leaf of the given tree. We'll successfully delete that item, but the result of doing so is an empty tree - so its parent will contain an empty tree in its subtrees list. Ex. 
```python
>>> t = Tree(10, [Tree(1, []), Tree(2, []), Tree(3, [])])  # A tree with leaves 1, 2, and 3
>>> t.delete_item(1)
True
>>> t.delete_item(2)
True
>>> t.delete_item(3)
True
>>> t._subtrees
[<__main__.Tree object at 0x081B4770>, <__main__.Tree object at 0x081B49F0>, <__main__.Tree object at 0x0845BB50>]
>>> t._subtrees[0].is_empty() and t._subtrees[1].is_empty() and t._subtrees[2].is_empty()
True
```
Our tree `t` now has three empty *subtrees*! This is certainly unexpected, and depending on how we've written our `Tree` methods, this may cause errors in our code. At the very least, these empty subtrees are taking up unnecessary space in our program, making it slower
### Fixing the Problem
Now we will change our previous deletion code, so that if we detect a leaf, we should remove the now-empty subtree from its parent's subtree list. This involves only a very small code change in `delete_item`
```python
def delete_item(self, item: Any) -> Bool:
    if self.is_empty():
        return False              # item is not in the tree
    elif self._subtrees == []:
        if self._root != item:    # item is not in the tree
            return False
        else:                     # resulting tree should be empty
            self._root = None
            return True
    else:
        if self._root == item:
            self._delete_root()   # delete the root
            return True
        else:
            for subtree in self._subtrees:
                deleted = subtree.delete_item(item)
                if deleted and subtree.is_empty():
                    # The item was deleted and the subtree is now empty.
                    # We should remove the subtree from the list of subtrees.
                    # Note that mutating a list while looping through it is
                    # EXTREMELY DANGEROUS!
                    # We are only doing it because we return immediately
                    # afterwards, and so no more loop iterations occur.
                    self._subtrees.remove(subtree)
                    return True
                elif deleted:
                    # The item was deleted, and the subtree is not empty.
                    return True

            # If we don't return inside the loop, the item is not deleted from
            # any of the subtrees. In this case, the item does not appear
            # in <self>.
            return False
```
As it says in the code, mutating a list while using it is **extremely dangerous**, we avoid this problem here right after removing the subtree, we stop the method by returning `True`.

Removing all those empty subtrees was annoying, so if we want we can just include it to our representation invariant. 
```python
class Tree:
    # === Private Attributes ===
    _root: Optional[Any]
    _subtrees: list[Tree]

    # === Representation Invariants ===
    # - If self._root is None then self._subtrees is an empty list.
    #   This setting of attributes represents an empty tree.
    #
    #   Note: self._subtrees may be empty when self._root is not None.
    #   This setting of attributes represents a tree consisting of just one value.
    #
    # - (NEW) self._subtrees does not contain any empty trees.
```
#148practice If you want further practice, consider the `insertion`, or what if we wanted our `delete` to remove all subtrees as well?

# 6.4 - Introduction to Binary Search Trees
We’re going to learn about a new data structure called the **Binary Search Tree** (or BST). Binary search trees make certain operations fast, and are the basis of advanced data structures you’ll learn about in [CSC263](https://student.utm.utoronto.ca/calendar/course_detail.pl?Depart=7&Course=CSC263H5) that are even more efficient.

## The Multiset ADT
Our goal this week is to use the tree concept and apply them to the Multiset ADT, which supports the following interactions.
- check whether the collection is empty
- check whether a given item is in the collection
- add a given item to the collection
- remove a given item from the collection

### Searching in Lists
For lists, the obvious iterative algorithm for any kind of list is to loop through every item in the list and stop when an item is found. When the item isn't in the list, every item must be checked, making this a *linear time* operation.
It turns out that the `Tree.__contains__` method has the same behaviour: if the item is not in the tree, every item must be checked, so switching from lists to trees isn't enough improvement.

However, a great insight of computer science is that adding some additional structure to input data allows for new and more efficient algorithms. You have seen a simple form of this called *augmentation* in previous labs, but we will use more complex *structures* now. 

In the case of Python lists, if we assume the list is *sorted* we can then use the **binary search algorithm** to greatly improve the efficiency of the search. 
Previously we attempted this, and found that there was many drawbacks due to working with array-based lists, specifically, the insertion and deletion. Thus, we can use trees to solve this flaw.
## Binary Search Trees: Definitions
To do this, we will combine the branching structure of trees with the idea of binary search to develop a notion of a “sorted tree”, which we will call a **Binary Search Tree (BST)**.

A **binary tree** is a tree in which every item has at most two subtrees. An item in a binary tree satisfies the **binary search tree property** if its value is greater than or equal to all items in the left subtree, and less than or equal to all items in its right subtree. 
A binary tree is a **binary search tree** if _every_ item in the tree satisfies the binary search tree property (the “every” is important: in general, it’s possible that some items satisfy this property but others don’t).

it is important to note that **Binary Search Trees** naturally represent *sorted data*, that is, if the data doesn't arrive for insertion in sorted order, the BST keeps track of it in a sorted fashion.
BST's are extremely efficient in searching for an item; but unlike sorted Python lists, they can be far more efficient at insertion and deletion while maintaining sorting.

# 6.5 - Binary Search Tree Implementation and Search
Our implementation of a `BinarySearchTree` class is obviously based on the `Tree` implementation. The first difference is that since there are two subtrees, we know their left/right ordering matters; requiring attributes `_left` and `_right`.
```python
class BinarySearchTree:
    """Binary Search Tree class.

    This class represents a binary tree satisfying the Binary Search Tree
    property: for every node, its value is >= all items stored in its left
    subtree, and <= all items stored in its right subtree.
    """
    # === Private Attributes ===
    # The item stored at the root of the tree, or None if the tree is empty.
    _root: Optional[Any]
    # The left subtree, or None if the tree is empty.
    _left: Optional[BinarySearchTree]
    # The right subtree, or None if the tree is empty.
    _right: Optional[BinarySearchTree]
```
Another difference between the `BinarySearchTree` and the `Tree` is in how we distinguish between empty and non-empty trees. In the `Tree` class, an empty tree has a `_root` value of `None`, and an *empty list* `[]` for its list of subtrees. In the `BinarySearchTree` class, an empty tree also has a `_root` value of `None`, but its `_left` and `_right` attributes are set to `None` as well.
Moreover, for `BinarySearchTree`, an empty tree is the _only_ case where any of the attributes can be `None`; when we represent a non-empty tree, we do so by storing the root item (which isn’t `None`) at the root, and storing `BinarySearchTree` objects in the `_left` and `_right` attributes. The attributes `_left` and `_right` might refer to _empty_ binary search trees, but this is different from them being `None`!

Any method we add to the `BinarySearchTree` class (a) can rely upon these properties, and (b) must maintain these properties, since the other methods rely upon them. This is so important that we document them in our representation invariants, along with the BST property itself.

```python
    # === Representation Invariants ===
    #  - If _root is None, then so are _left and _right.
    #    This represents an empty BST.
    #  - If _root is not None, then _left and _right are BinarySearchTrees.
    #  - (BST Property) All items in _left are <= _root,
    #    and all items in _right are >= _root.
```
Now we will implement the initializer and `is_empty` methods for the class.
```python
def __init__(self, root: Optional[Any]) -> None:
	"""Initialize a new BST containing only the given root value.

	If <root> is None, initialize an empty BST.
	"""
	if root is None:
		self._root = None
		self._left = None
		self._right = None
	else:
		self._root = root
		self._left = BinarySearchTree(None)   # self._left is an empty BST
		self._right = BinarySearchTree(None)  # self._right is an empty BST

def is_empty(self) -> bool:
	"""Return whether this BST is empty.
	"""
	return self._root is None
```
We do not allow client code to pass in left and right subtrees as parameters to the initializer. This is because binary search trees have a much stronger restriction on where values can be located in the tree, and so a separate method is used to insert new values into the tree.

## Searching A Binary Search Tree
Recall that the key insight of the binary search algorithm in a sorted list is that by comparing the target item with the *middle* of the list, we can immediately cut in half the remaining items to be search. This is similar for a BST.
For general trees, the standard search algorithm is to compare the `item` against the root, and then search in each of the subtrees until either the item is found, or all the subtrees have been searched. When `item` is not in the tree, every item must be searched.
In stark contrast, for BSTs _the initial comparison to the root tells you which subtree you need to check_. That is, only one recursive call needs to be made, rather than two! (this is very cool, allows for faster then O(n) lookups)
```python
def __contains__(self, item: Any) -> bool:
	"""Return whether <item> is in this BST.
	"""
	if self.is_empty():
		return False
	else:
		if item == self._root:
			return True
		elif item < self._root:
			return item in self._left   # or, self._left.__contains__(item)
		else:
			return item in self._right  # or, self._right.__contains__(item)
```
While this code closely matches the empty-check for the general `Tree` class, we can combine the two levels of nested ifs to get a more concise version.
```python
def __contains__(self, item: Any) -> bool:
	"""Return whether <item> is in this BST.
	"""
	if self.is_empty():
		return False
	elif item == self._root:
		return True
	elif item < self._root:
		return item in self._left   # or, self._left.__contains__(item)
	else:
		return item in self._right  # or, self._right.__contains__(item)
```

# 6.6 - Mutating Binary Search Trees
Now that we know how searching works, we will study two mutating methods of the Multiset/Collection ADT: insertion and deletion.
The idea is simple, given an item to delete, we search for the item, if we find it, it will be at the root of some subtree and we will delete it. 
```python
def delete(self, item: Any) -> None:
	"""Remove *one* occurrence of <item> from this BST.

	Do nothing if <item> is not in the BST.
	"""
	if self.is_empty():
		pass
	elif self._root == item:
		self.delete_root()
	elif item < self._root:
		self._left.delete(item)
	else:
		self._right.delete(item)

def delete_root(self) -> None:
	"""Remove the root of this tree.

	Precondition: this tree is *non-empty*.
	"""
```
We are once again using the helper method strategy, using `delete_root`, to pull part of the required functionality thats a bit tricky.
```python
def delete_root(self):
	if self._left.is_empty() and self._right.is_empty():
		self._root = None
		self._left = None
		self._right = None
```
Now you may wonder, what about the case where `self._right` is not empty or `self._left`?
We can analyze the tree structure for two easy special cases.
```python
def delete_root(self) -> None:
	if self._left.is_empty() and self._right.is_empty():
		self._root = None
		self._left = None
		self._right = None
	elif self._left.is_empty():
		# "Promote" the right subtree.
		self._root, self._left, self._right = \
			self._right._root, self._right._left, self._right._right
	elif self._right.is_empty():
		# "Promote" the left subtree.
		self._root, self._left, self._right = \
			self._left._root, self._left._left, self._left._right
```
Finally, we need to add the case for when both subtrees aren't empty. Rather than restructuring the tree, we can fill the "hole" at the root by *replacing* the root item with another value from the tree.
The key insight is that there are **only two values** we could replace it with and still maintain the BST property: the maximum (or, rightmost) value in the left subtree, or the minimum (or, leftmost) value in the right subtree. We’ll pick the left subtree here.
```python
def delete_root(self) -> None:
	if self._left.is_empty() and self._right.is_empty():
		self._root = None
		self._left = None
		self._right = None
	elif self._left.is_empty():
		# "Promote" the right subtree.
		self._root, self._left, self._right = \
			self._right._root, self._right._left, self._right._right
	elif self._right.is_empty():
		# "Promote" the left subtree.
		self._root, self._left, self._right = \
			self._left._root, self._left._left, self._left._right
	else:
		self._root = self._left.extract_max()

def extract_max(self) -> object:
	"""Remove and return the maximum item stored in this tree.

	Precondition: this tree is *non-empty*.
	"""
```
Now, we've used another helper function `extract_max`. Finding the max item is easy: just keep traversing right for bigger values. Furthermore, removing the max is easier than BST deletion as the max has at most 1 child.
```python
def extract_max(self) -> object:
	"""Remove and return the maximum item stored in this tree.

	Precondition: this tree is *non-empty*.
	"""
	if self._right.is_empty():
		max_item = self._root
		# Once again, "Promote" the left subtree.
		self._root, self._left, self._right = \
			self._left._root, self._left._left, self._left._right
		return max_item
	else:
		return self._right.extract_max()
```
### One Deletion Exercise

Try implementing `delete_all`, which is similar to `delete_item` except that it deletes _all_ occurrences of an item from a BST. Think carefully about how to handle duplicate elements!

# 6.7 - Binary Search Trees and Running Time
Now we return for the only reason we actually wanted a BST, a more efficient run time which supports search, insertion, and deletion.
The implementation of `__contains__`, `insert`, and `delete` for BSTs all have the same structure, in that they all make just one recursive call inside the recursive step (they each use the BST property to decide which subtree to recurse into). Let’s focus on `__contains__` here.

```python
def __contains__(self, item: Any) -> bool:
	"""Return whether <item> is in this BST.
	"""
	if self.is_empty():
		return False
	else:
		if item == self._root:
			return True
		elif item < self._root:
			return item in self._left   # or, self._left.__contains__(item)
		else:
			return item in self._right  # or, self._right.__contains__(item)
```

In this code, every recursive call goes one level down into the tree, so the max number of calls that can be made is equal to the height of the tree plus 1, since the extra call comes because our implementation includes the empty subtree of a leaf.
Since each line of code inside `__contains__` _except_ the recursive call runs in constant time (i.e., doesn’t depend on the size of the tree), the total running time is proportional to the number of recursive calls made.

Because we argued the max number of recursive calls is roughly the height of the tree, we could say the running time is **O(h)**, where h is the height of the tree. However, this is only partially correct. In fact, if we "get lucky" and search for the root item of the tree, we only ever make one comparison.

## Worst-case vs Best-case Running Time
So far, we've focused on how running time depends on the *size* of its inputs, however, very often the case is that even for a fixed input size, the running time varies depending on some other properties of the inputs. ex. searching for the root item of a BST vs. searching for an item very deep in a BST.
We know that where *h* is the height of the tree, the time taken always is equal to *h + 1* 
We define the **worst-case running time** of an algorithm as a function _WC(n)_, which maps an input size _n_ to the _maximum_ possible running time for all inputs of size _n_. What we colloquially refer to as the “worst case” for an algorithm is actually a **family of inputs**, one per input size, where each input is one that results in the maximum running time for its size.
Since the worst-case running time is a function, we can describe it using our Big-Oh notation. We can say that for BST search, the _worst-case_ running time is _O(h)_, where _h_ is the height of the tree.
Similarly, in Big-Oh notation, we can say that the best case is *O(1)*, since `__contains__` immediately verifies that the root is equal to the item we're searching for.

## Tree Height and Size
You may believe that array/linked lists, since they are O(n) for searching, are equal to O(h) in the BST's, but this is where our choice of variables truly matters.
We know that BST search is, in the worst case, proportional to the height of the BST; but remember the height of a BST can be much smaller than its size. 
If we consider a BST with n items, its height can be as large as n (much like a list) or as small as *log(n)*. Why? To put it another way, a tree height of h can have at most *2^h - 1* items, so if we have n items to store, we need at least *log(n)* height to store them. 

In the later course, CSC263, _Data Structures and Analysis_, you will explore more sophisticated insertion and deletion algorithms that ensure that the height is always logarithmic, thus guaranteeing the efficiency of these operations!

### In Class Exercise - Preorder Searching
```python
class BinarySearchTree():
	def preorder(self, act: Callable[[Tree], Any]) -> None:
		""" Visit each node of the tree in preorder, and perform an            action on the nodes that are visted, using the function <act>
		"""
		act(self)
		for subtree in self.subtrees:
			subtree.preorder(act)
	def postorder(self, act: Callable[[Tree], Any]) -> None:
		for subtree in self.subtrees:
			subtree.preorder(act)
		act(self)
```

# 6.8 - Expression Trees
We're going to wrap up our study of tree-based data structures. We will analyze one vibrant application of trees: *representing programs*. Picture a Python program with a few classes, functions, and dozens or hundreds of lines of code. As humans, we read and write code as *text*, and we take for granted the fact that we can ask the computer to run our code. 
What happens when we "run" a program? Another program called the *Python Interpreter* is responsible for taking the file and running the program. Writing programs with direct text is hard, as strings are fundamentally *linear* structures, but programs like **Python** have a fundamentally *recursive* structure. 
So the first step that the Python interpreter takes when given a file to run is to _parse_ the text from the file and create a new representation of the program, called an _Abstract Syntax Tree (AST)_.[1](https://mcs.utm.utoronto.ca/~148/course/notes/trees/expression_trees.html#fn1) The “Tree” part is significant: given the recursive nature of Python programs, it is natural that we’ll use a tree-based data structure to represent them!

This week, we will explore the basics of modeling programs using tree-based data structures, trying to do some simple *expressions*.

## The `Expr` Class
In Python, an *expression* is a piece of code which is meant to be evaluated, returning the value of that expression. 

Expressions are the basic building blocks of the language and are necessary for computing anything. But because of the immense variety of expression in Python, we can't use a single class for all of the expressions, but we can use inheritance to ensure that they follow the same fundamental interface. 
Let's begin with an abstract class for expressions.
```python
class Expr:
    """An abstract class representing a Python expression.
    """
    def evaluate(self) -> Any:
        """Return the *value* of this expression.

        The returned value should be the result of how this expression would be
        evaluated by the Python interpreter.
        """
        raise NotImplementedError
```
Notice that we haven't specified any attributes for this class, as each type of expression will use different attributes. Let's begin with two expression types 

### Num: Numeric Constants
The simplest Python expression is a *literal* constant like `3` or `'hello'`. We'll start by representing numeric constants (`int`s and `float`s). This is relatively simple, with a single attribute representing the value of the constant.
```python
class Num(Expr):
    """An numeric constant literal.

    === Attributes ===
    n: the value of the constant
    """
    n: Union[int, float]

    def __init__(self, number: Union[int, float]) -> None:
        """Initialize a new numeric constant."""
        self.n = number

    def evaluate(self) -> Any:
        """Return the *value* of this expression.

        The returned value should be the result of how this expression would be
        evaluated by the Python interpreter.

        >>> number = Num(10.5)
        >>> number.evaluate()
        10.5
        """
        return self.n  # Simply return the value itself!
```
You can think of constants as being the base case, or leaves, of an abstract syntax tree. Next, we'll look to combine these in larger expressions

### `BinOp` Arithmetic Operations
The obvious way to combine numbers is through standard arithmetic operations. In Python, an *arithmetic operation* is an expression that consists of three parts: a left and right subexpression (two operands) and the operator itself. We'll represent this as such.
```python
class BinOp(Expr):
    """An arithmetic binary operation.

    === Attributes ===
    left: the left operand
    op: the name of the operator
    right: the right operand

    === Representation Invariants ===
    - self.op == '+' or self.op == '*'
    """
    left: Expr
    op: str
    right: Expr

    def __init__(self, left: Expr, op: str, right: Expr) -> None:
        """Initialize a new binary operation expression.

        Precondition: <op> is the string '+' or '*'.
        """
        self.left = left
        self.op = op
        self.right = right
```
Note that `BinOp` class is a binary tree! Its "root" value is the operator name (stored as the attribute op), while its left and right "subtrees" represent the two *operand subexpressions*.
For example, we could represent `3 + 5.5` like this.
```python
BinOp(Num(3), '+', Num(5.5))

Prep10
BinOp(Num(3), '+', Num(4))
BinOp(BinOp(Num(3), '+', Num(4)), '*', Num(6))
BinOp(Num(1.1), '+', BinOp(Num(2.2), '+', BinOp(Num(3.3), '+', Num(4.4))))

```

But of course, the types of `left` and `right` attributes aren't `Num`, they're `Expr` - so either of these can be `BinOp`s as well:
```python
# ((3 + 5.5) * (0.5 + (15.2 * -13.3)))
BinOp(
    BinOp(Num(3), '+', Num(5.5)),
    '*',
    BinOp(
        Num(0.5),
        '+',
        BinOp(Num(15.2), '*', Num(-13.3)))
```
A computer program like the Python interpreter cannot understand this expression like humans do in their heads, and this is where the tree-like structure of `BinOp` shines. 
To *evaluate* a binary operation, we first evaluate its left and right operands and then combine them using the specified arithmetic operator. 
```python
class BinOp:
    def evaluate(self) -> Any:
        """Return the *value* of this expression.
        """
        left_val = self.left.evaluate()
        right_val = self.right.evaluate()

        if self.op == '+':
            return left_val + right_val
        elif self.op == '*':
            return left_val * right_val
        else:
            raise ValueError(f'Invalid operator {self.op}')
```
#### The Subtle Recursive Structure of Expression Trees
Even though the above code appears simple, it uses recursion in a pretty subtle way. Notice that we're making pretty normal-looking recursive calls `self.left.evaluate()` and `self.right.evaluate()`, matching the tree structure of `BinOp1`... But where is the *base case(s)*?
This is the most significant difference between expression trees and the tree-based classes we've studied. Because we're using multiple subclasses of `Expr`, there are *multiple* `evaluate` methods, one in each subclass. 
Every time `self.left.evaluate` and `self.right.evaluate` are called, they could either refer to `BinOp.evaluate` _or_ `Num.evaluate`, depending on the types of `self.left` and `self.right`.

In particular, notice that `Num.evaluate` doesn't make any subsequent calls to `evaluate` since it just returns the objects `n` attribute.
So fundamentally, `evaluate` is still an example of structural recursion, just one that spans multiple `Expr` subclasses. 

## Extra Abstract Syntax Tree Studies
It turns out that there is a built-in Python library called `ast` (short for “abstract syntax tree”) that uses the same approach we’ve covered here, but, of course, it is comprehensive enough to cover the entire spectrum of the Python language. If you’re interested in reading more about this, feel free to check out some excellent documentation at [https://greentreesnakes.readthedocs.io](https://greentreesnakes.readthedocs.io).


