# 7.1 - Recursive Sorting Algorithms
We're going to talk about a very specific data-processing operation, which is fundamental in computer science: **sorting**. Just as we saw multiple data structures that could be used to represent the same ADT, we'll look at a few different ways to implement sorting. 
We've seen sorting before in CSC108; with many basic sorting algorithms such as bubblesort, selection sort, and insertion sort, which have all been *interative*, meaning they involved multiple loops through the list.
We've talked about how all their running times were *quadratic* in the size of the list, so each of these algorithms sorts a list of size n in O(n^2) steps. 

Now we will cover **mergesort** and **quicksort**, which are both recursive "divide and conquer" algorithms which are a general class of algorithms that use the following steps.
1. Split up the input into two or more parts.
2. Recurse on each part separately.
3. Combine the results of the previous step into a single result.
Where these two algorithms differ is in the splitting and combining: mergesort does the “hard” (algorithmically complex) work in the combine step, and quicksort does it in the divide step.
## Mergesort
The first algorithm is called **mergesort** which uses the "divide and conquer" philosophy very literally. The basic idea of this algorithm is that it divides its input list into two halves, recursive sort each half, and then merges each sorted half into the final sorted list.
```python
def mergesort(lst: list) -> list:
    """Return a sorted list with the same elements as <lst>.

    This is a *non-mutating* version of mergesort; it does not mutate the
    input list.
    """
    if len(lst) < 2:
        return lst[:]
    else:
        # Divide the list into two parts, and sort them recursively.
        mid = len(lst) // 2
        left_sorted = mergesort(lst[:mid])
        right_sorted = mergesort(lst[mid:])

        # Merge the two sorted halves.
        return _merge(left_sorted, right_sorted)
```

### The Merge Operation
While this code looks very straightforward, we've hidden the main complexity in the helper function `_merge`, which needs to take two lists and combine them into one sorted list. 
For two arbitrary lists, there isn't an "efficient" way of combining them. For example, the first element of the returned list should be the minimum value in either list, and to find this value we'd need to iterate through each element in both lists. 

The issue is, we assume that the two lists are *sorted*, which changes things dramatically. For example, to find the minimum item of `lst1` and `lst2` when both lists are sorted, we *only* need to compare `lst1[0]` and `lst2[0]`, since the minimum must be one of these two values. We can generalize this idea so that *after every comparison we make, we can add a new element to the sorted list.* This is the key insight that makes the `_merge` operation efficient, and which gives the algorithm `mergesort` its name. 
```python
def _merge(lst1: list, lst2: list) -> list:
    """Return a sorted list with the elements in <lst1> and <lst2>.

    Precondition: <lst1> and <lst2> are sorted.
    """
    index1 = 0
    index2 = 0
    merged = []

    while index1 < len(lst1) and index2 < len(lst2):
        if lst1[index1] <= lst2[index2]:
            merged.append(lst1[index1])
            index1 += 1
        else:
            merged.append(lst2[index2])
            index2 += 1

    # Now either index1 == len(lst1) or index2 == len(lst2).
    assert index1 == len(lst1) or index2 == len(lst2)
    # The remaining elements of the other list
    # can all be added to the end of <merged>.
    # Note that at most ONE of lst1[index1:] and lst2[index2:]
    # is non-empty, but to keep the code simple, we include both.
    return merged + lst1[index1:] + lst2[index2:]
```

## Quicksort
While **quicksort** uses a "divide and conquer" approach, it takes a different philosophy for dividing up its input list. Here's some intuition for this approach: suppose we're sorting a group of people alphabetically by their surname. We do this by first dividing up the people into two groups: those whose surnames start with A-L, and those who start with M-Z. This can be seen as an "approximate sort". Even though each smaller group is not sorted, we do know that everyone in the A-L group should come before everyone in the M-Z group. Then, after sorting each group, we're done: simply take the groups and concatenate them to obtain the fully sorted list.

Formally, *quicksort* algorithm uses this exact idea:
1. First, it picks some element in its input list and calls it the **pivot**.
2. It then splits up the list into two parts: the elements less than or equal to the pivot, and those greater than the pivot.[2](https://mcs.utm.utoronto.ca/~148/course/notes/recursive-sorting/recursive_sorting.html#fn2) This is traditionally called the _partitioning_ step.
3. Next, it sorts each part recursively.
4. Finally, it concatenates the two sorted parts, putting the pivot in between them.
```python
def quicksort(lst: list) -> list:
    """Return a sorted list with the same elements as <lst>.

    This is a *non-mutating* version of quicksort; it does not mutate the
    input list.
    """
    if len(lst) < 2:
        return lst[:]
    else:
        # Pick pivot to be first element.
        # Could make lots of other choices here (e.g., last, random)
        pivot = lst[0]

        # Partition rest of list into two halves
        smaller, bigger = _partition(lst[1:], pivot)

        # Recurse on each partition
        smaller_sorted = quicksort(smaller)
        bigger_sorted = quicksort(bigger)

        # Return! Notice the simple combining step
        return smaller_sorted + [pivot] + bigger_sorted
```
Thankfully, implementing the `_partition` helper function is simpler than the `_merge` helper, as we can just do it using one loop through the list.
```python
def _partition(lst: list, pivot: Any) -> tuple[list, list]:
    """Return a partition of <lst> with the chosen pivot.

    Return two lists, where the first contains the items in <lst>
    that are <= pivot, and the second is the items in <lst> that are > pivot.
    """
    smaller = []
    bigger = []

    for item in lst:
        if item <= pivot:
            smaller.append(item)
        else:
            bigger.append(item)

    return smaller, bigger
```
#### Note
Choosing the *first* element to be the pivot in quicksort has some significant drawbacks regarding efficiency.

# 7.2 - Efficiency of Recursive Sorting Algorithms
Iterative algorithms are typically quite straightforward to analyze, as long as you can determine precisely how many iterations each loop will run. Recursive code can be trickier, because not only do you need to analyze the running time of the non-recursive part of the code, but you must also factor in the cost of each recursive call made. 
You'll learn how to do this formally in CSC236, but for this course, we're going to see a nice visual intuition for analyzing simple recursive functions

## Mergesort
Recall the mergesort algorithm:
```python
def mergesort(lst):
    if len(lst) < 2:
        return lst[:]
    else:
        mid = len(lst) // 2 # This is the midpoint of lst
        left_sorted = mergesort(lst[:mid])
        right_sorted = mergesort(lst[mid:])

        return _merge(left_sorted, right_sorted)
```

Suppose we call `mergesort` on a list of length *n*, where n >= 2. We first analyze the running time of the code in the branch other than the recursive calls themselves:
- The “divide” step takes linear time, since the list slicing operations `lst[:mid]` and `lst[mid:]` each take roughly _n/2_ steps to make a copy of the left and right halves of the list, respectively.[1](https://mcs.utm.utoronto.ca/~148/course/notes/recursive-sorting/recursive_sorting_efficiency.html#fn1)
- The `_merge` operation also takes linear time, that is, approximately _n_ steps (why?).
- The other operations (calling `len(lst)` arithmetic, and the act of `return`ing) all take constant time, independent of _n_.

Putting this together, we say that the running time for the *non-recursive* part of this algorithm is *O(n)*, or *linear* concerning the length of the list. 
So far, we've ignored the cost of the two recursive calls. Well, since we split the list in half, each new list on which we make a call has a length *n/2*, and each of those merge and slice operations will take approximately *n/2* steps, and then these two recursive calls will make four recursive calls, each on length *n/4*, etc. 
We can represent the total cost as a big tree, which recursively divides such that the second level is two recursive calls, the third is four, etc. until we reach the base case of list length 1.
![[Pasted image 20250320124838.png]]Note that even though it looks like the tree shows the size of the lists at each recursive call, what it's showing is the *running time of the non-recursive part of each recursive call*, which just happens to be *approximately equal* to the size of the list.

The height of this tree is the **recursion depth**: the number of recursive calls that are made before the base case is reached. Since for mergesort we start with a list of length *n* and divide the length by 2 until we reach list length 1, the recursive depth of mergesort is the number of times you need to divide *n* by 2 to get 1. In another way, it's the number _k_ such that _2^k ≈ n_. Remember logarithms? This is precisely the definition: _k ≈ log n_, and so there are approximately _log n_ levels.[2](https://mcs.utm.utoronto.ca/~148/course/notes/recursive-sorting/recursive_sorting_efficiency.html#fn2)
Finally, notice that at each level, the total cost is *n*. This makes the total cost of mergesort *O(n log n)* which is *far* more efficient than the quadratic runtime of insertion and selection sort, where n gets very large.
The _worst-case_ and _best-case_ Big-Oh running times for mergesort are the same: _O(n log n)_.
Note: we omit the base of a logarithm in computer science, always assuming base 2.

## The Perils of Quicksort
What about quicksort? Is it possible to do the same analysis? Not quite. 
The key difference is that with mergesort, we are always split into two halves, whereas this isn't necessarily the same for quicksort.
Suppose we get lucky, and we choose the median value as our pivot, such that it's always roughly *n/2* partitions. Then, the analysis is the same as mergesort and it is also *n log n* runtime. 

What if we get extremely unlucky with our pivot choice, and instead, we choose the smallest element? Then the partitions are as uneven as possible, with one having no elements and the other having size *n - 1*. We get a tree like this. 
![[Pasted image 20250320125721.png]]
Here, the recursion depth is *n* (the size decreases by 1 at each recursive call), so adding the cost of each level gives us the expression 
*(n - 1) + [(n + (n - 1) + (n - 2) + ... + 1]* = *(n - 1) + n(n + 1)/2*
Making it have a worst-case quadratic runtime. Thus, we conclude that its best-case is *O(n log n)* and its worst case is *O(n^2)*

### Quicksort in the "real world"
You might wonder if quicksort is truly used in practice if its worst-case performance is so much worse than mergesort's. Keep in mind that we’ve swept a lot of details under the rug by saying that both `_merge` and `_partition` take _O(n)_ steps—the Big-Oh expression ignores constant factors, and so _5n_ and _100n_ are both treated as _O(n)_ even though they are quite different numerically.
In practice, the non-recursive parts of quicksort can be significantly faster than the non-recursive parts of mergesort. For most inputs, quicksort has “smaller constants” than mergesort, meaning that it takes a fewer number of computer operations, and so performs faster.
In fact, with some more background in probability theory, we can even talk about the performance of quicksort on a _random_ list of length _n_; it turns out that the average performance is _O(n log n)_—with a smaller constant than mergesort’s _O(n log n)_—indicating that the actual “bad” inputs for quicksort are quite rare. You will see this proven formally in later courses, such as CSC263.