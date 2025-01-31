#csc108
Implement bubblesort and time how long it takes to sort lists of various lengths. Compare the result to selection and insertion sort
```python
def bubblesort(lst: list) -> None:
	# takes an unsorted list - each iteration it compares the current value to
	# the next
	while index > 0 and lst[index-1] > 0 :
		if lst[i] < lst[i+1]:
			lst[i], lst[i+1] = lst[i+1], lst[i]
		

def checker(lst: list) -> list:
	for i in range(lst):
		bubblesort(lst)
```

##### Intro to Time Complexity
- When working with time complexity, assume large input
```python
# Linear Time
for elm in L:
	for i in range(10):
		print(elm)

n = len(L) # 1
index = 0 # 1
while index < len(L): # n
	index += 1 # n 

# Quadratic Time
n = len(L)
index = 0
while index < len(L):
	index2 = 0
		while index2 < len(L):
			index1, index2 += 1


# Logorithmic Time
def log(L:list[int]):
	n = len(L) # 1
	index = 0 # 1
	while 2** index < len(L) # log[2](n)
		index += 1 # log[2](n)
# 2log[2] + 2 = O(log(n))
# Assume base 2
```