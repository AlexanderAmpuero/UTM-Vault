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