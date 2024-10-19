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
##### == v. is
```python
>>> a = [1, 2, 3, 4, 5]
>>> b = [1, 2, 3, 4, 5]
>>> b == a
True
>>> b is a
False
```
- "b is a" checks whether or not b and a have the same id in the system memory
- "b == a" checks whether or not the values within b and a are the same

##### List Edge Cases
```python
>>> L = [1,2,3,4,5]
>>> print(L[4:2])
[]
>>> print(L[4:2:-1])
[5, 4]
>>> print(L[5:2:-1])
[5, 4]

>>> def count_duplicate_v1(lst: List[int]) -> int:
		count = 0
		for i in range(len(lst) - 1):
			if i == lst[i+1]:
				count += 1
		return count
```

- The above statement is a test to see what the list outputs when read in reverse
- the second function counts the amount of duplicate, be sure to use the range(len(lst)) part

##### Writing a sum Function
```python
>>> def sum_digits(n: int) -> int:
>>> 	sum = 0
>>> 	while n > 0:
>>> 		total += (n%10)
>>> 		n //= 10
>>> 	return total
```
- The above function uses n%10 to take the last digit of the n value
- Then integer divides to become 0
#### Explain Your Doctests!

##### Lists, Lists, and more Lists
```python
>>> xs = [[1,2],[5,6,7]]
>>> len(xs)
2
>>> nested_lst = [[1,2,3], ["a", "b", "c"], True]
>>> for sublist in nested_list:
>>> 	for item in sublist:
>>> 		print(item, end = " ")
#TypeError: 'bool' not interable


```

- This error occurs because you cannot print each char in True because it is a boolean

```python
# Good Question
>>> def enrolled_teams(team: list[list[int]], students: list[int]) -> bool:  
	    for i in team:  
	        for j in i:  
	            if j not in students:  
	                return False  
	    return True
>>> def list_range(a: int, b: int, c: int) -> list[int]:
```