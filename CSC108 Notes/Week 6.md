# Term Test 1 Review

- 17 pages
- 90 minutes 
- Don't study with readings
- Study with Piazza

# List Review
##### Empty List
```python
lst = []
type(lst)

>>> len(lst)
>>> 0
```
- Can be used for storage when using operations

##### List Manipulation
```python
>>> s = 'abc'
>>> s[1] = z

>>> s = ["a", "b", "c"]
>>> for elements in s
>>> 	print(elements)
...
a
b
c
>>> [] < [1]
True
```
- You cannot manipulate a list unless you use special functions
- An empty set is less than any set

##### append() v. extend() and sort()
```python
>>> lst = [99, 7, 3, 0]
>>> lst.sort()
>>> lst
[0, 3, 7, 99]

>>> lst.append([100, 1])
>>> lst
[0, 3, 7, 99, [100, 1]]

>>> lst.extend([4, 5])
>>> lst
[0, 3, 7, 99, [100, 1], 4, 5]
```
- extend() adds each list item to the list
- append() adds each together
- sort() arranges the items from smallest to largest
##### Alias v. Copy
```python
>>> lst = [1, 2, 3]
>>> lst_alias = lst
>>> lst.append(4)
>>> lst_alias
[1, 2, 3, 4]
```
```python
>>> lst = [1, 2, 3]
>>> lst_alias = lst.copy()
>>> lst.append(4)
>>> lst_alias
[1, 2, 3]

```
- Both lst_alias and lst have the same id, meaning anything that effects one, effects the other
- The copy function replicates the list, but does not give them the same id, this allows them to still exist as separate elements.
##### Type Annotations
```python
# Task 1
def counter(xs: list[int], ys: list[int]) -> int:
	count = 0
	for i in xs:
		for j in ys:
			if i == j:
				count += 1
	return count

# Task 2
def make_unique(xs: list[int]) -> list[int]:
	unique_lst = []
	for i in xs:
		if i not in unique_lst:
			unique_lst.append(i)
	return unique_lst
```
