##### List Examples
```python
>>> L = [1,2,3,4,5]
>>> print(L[5:2])
>>> 

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