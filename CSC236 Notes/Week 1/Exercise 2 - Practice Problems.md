### Problems
![[Pasted image 20250910200246.png]]

```python
# Pre(n): n in naturals and n >= 2
# Return b s.t. Post(b,n): b in naturals and ... (omitted)
def algo(n):
	# size(n) = n
	# valid measure because n in naturals: by Pre(n)
	
	# base cases
	if n == 2: return 6
	elif n == 3: return 9 # changed to elif
	elif n == 4: return 12 # changed to elif
	
	else: # NOTE: (C) is a convenient default label we use for conditions
		# (C) n >= 5 because n >= 2 and n != 2,3,4
		
		# *state why is doesn't violate pre(n) and is a decreasing set*
		# *i.e. a2 < a1*
		
		# Valid call: because n - 2 >= 5 - 2 >= 2 (by C and Pre) so 
		# n - 2 is in naturals
		# Valid recursively: because size(n - 2) = n - 2 < n = size(n)
		a = algo(n - 2)
		return a + 6
```

`Valid call: because n - 2 >= 5 - 2 >= 2 (by C and Pre)` this proves that it is a valid call, not violating the precondition
`Valid recursively: because size(n - 2) = n - 2 < n = size(n)` this proves that is is a decreasing set

![[Pasted image 20250911223930.png]]

```python
# Pre(n): n in naturals and n >= 2
# Return b s.t. Post(b, n): b in naturals and ...
def algo(n):
	# size(n) = n
	# valid measure as n in naturals: By Pre
	if n == 2:
		return 2 * 2
	else
		# (C) n > 2 because n >= 2 by Pre, and n != 2
		
		# This is not valid recursively nor a valid call
		# ex. algo(3) = algo(2) + algo(1)
		#     This immidiately violates Pre(n) as n == 1
		#     Furthermore, it causes infinite loop as base case is never reached
		r = algo(n - 1)
		s = algo(n - 2)
		return r + s
```
 Note: Didn't need to write all this, just provide a case where it fails. ex. algo(3)

![[Pasted image 20250911225358.png]]
```python
# Pre(n): n in naturals+
# Return r s.t. Post(r, n): r in naturals and ...
def aa(n):
	# size(n) = n
	# valid measure as n in naturals by Pre
	
	if n == 1: return 1
	else:
		# (C) n > 1 because n >= 1 by Pre and n != 1
		
		# Valid Call because n > 1 = sqrt(n) > 1 = floor(sqrt(n)) > 1 >= 1
		# also sqrt(n) > 1 implies floor(sqrt(n)) in naturals+ 
		# Satisfying Pre(n) and C
		
		# Valid Recursively because sqrt(n) < n (because n > 1 by C) and
		# n in integers
		# Thus size(floor(sqrt(n))) = floor(sqrt(n)) < n = size(n)
		r = aa(floor(sqrt(n)))
		return r**2 + 2 * r
```
My answer was correct, but not as rigorous. The above is the more rigorous solution. 

