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
	
	else: # added else statement to encapsulate recursion
	
		# *insert recursive structure proof here*
		
		# *state why is doesn't violate pre(n) and is a decreasing set*
		# *i.e. a2 < a1*
		a = algo(n - 2)
		return a + 6
```
