### 1. Prove that the following algorithm is correct
When we say that a string "ends with an even number of '*b*'s" we count every '*b*' at the right end of the string. For example, the string 'ababbb' does **not** end with an even number of '*b*'s, but the string '*bb*' does.

```python
# Pre(s): s is a string containing only 'a's and 'b's
# Return c s.t. Post(c, s): c in boolean, and c iff s ends with
# an even number of 'b's.
def g(s):
	# size(s) = len(s)
	# valid measure as s is a str, with len(s) in naturals
	
	# RH: c is a boolean, and c iff s ends with an even number of 'b's
	
	# valid if as '==' operator returns boolean. 
	# valid comparison '==' of s and '' because s is a string
	if s == '': return True
	# valid return value as '' contains an even number of 'b's, being 0. 
	# Thus true
	
	else:
		# (C) len(s) >= 1 and s contains only elements from set (a, b)
		
		# valid call because s[:-1] is a string of len(s[:-1]) >= 0
		# and s[:-1] contains only elements from (a, b) by Pre
		
		# valid recursively because size(s[:-1]) = len(s[:-1]) = len(s) - 1
		# < len(s) = size(s)
		c = g(s[:-1])
		# By RH, c is a boolean and c iff s[:-1] ends with even number of 'b's
		
		# valid if as '==' operator returns boolean. 
		# valid comparison '==' of s[-1] and 'a' because s[-1] is a string 
		# of len(1)
		if s[-1] == 'a': return True
		# valid return, as if s[-1] = 'a', then s ends with 0 'b's, which 
		# is an even number of 'b's. Thus by RH, c is true iff s ends with an
		# even number of 'b's, meaning c is true. 
		
		# valid 'not' operator, as c is a boolean, thus not c is the 
		# inverse of c
		else: return not c
		# (C) s[-1] = 'b', becuase s is not empty by base case and 
		# s[-1] != 'a' by previous case. 
		
		# valid return, as,
		# s = s[:-1] + 'b', ending with another b, thus implying, 
		# s ends with even number of b's
		# iff s[:-1] ends with odd number of b's
		# iff s[:-1] does not end with even number of b's
		# iff not c
```

### 2. Prove the following algorithm is correct
Note that the entire algorithm consists of *two* functions (`countless` and `helper`) that depend on each other. we will prove both functions are correct.
Furthermore, note that,
![[Pasted image 20250923004616.png]]
```python
# Pre(x, L): x is a comparable value, and L is a non-empty list of 
# comparable values
# Return c s.t. Post(c, x, L): c in naturals and c = the number of elements
# in L that are smaller than x

def countless(x, L):
	# Pre(b, e): b, e in naturals, and 0 <= b < e <= len(L)
	# return c s.t. Post(c, b, e): c in naturals and c = the number of elements
	# in L[b:e] that are smaller than x.
	
	def helper(b, e):
		# size(b, e) = e - b
		# valid measure because e in naturals and e > b by Pre
		
		# Assume RH: if a valid recursive call returns, it returns the correct
		# value
		
		# valid if and '==' operator because '==' returns boolean
		# valid '==' comparison because e and b are naturals where b < e by Pre 
		if e == b + 1:
			# valid if and '<' operator because '<' returns boolean
			# valid '<' comparison because L is a list of comparable values
			# and b is a natural number thats < len(L) by pre, 
			# and x is a comparable value
			
			if L[b] < x: return 1
			# valid return as 1 = L[b:e] => 1 = L[b:b+1], implying in a list of 
			# length 1, there is one element less than x.
			
			else: return 0
			# valid return as L[b] >= x implies that within L[b:e] = L[b:b+1],
			# there are no values less than x. Hence, 0 is returned
		else:
			# (C) len(L) >= e > b >= 0 by Pre, and e > b + 1 by base case
			
			# valid arithmetic because b and e are natural numbers by Pre
			c = (2*b + e + 2) // 3
			
			# c - b = floor((2*b + e + 2) / 3) - b
			# = floor((2*b + e + 2) / 3 - 3b/3)
			# = floor((-b + e + 2) / 3)
			# = floor((e - b + 2) / 3)
			# >= floor((2+2)/3) = 1
			# e - c = e - floor((2b + e + 2)/3)
			# = e + roof(-(2b + e + 2)/3)
			# = roof(3e/3 - 2b - e - 2)/3)
			# = roof((2e - 2b - 2)/3)
			# = roof((2e - 2b - 2)/3)
			# = roof((2(e-b) - 2)/3)
			# >= roof((2*2 - 2)/3) = 1
			
			# so b < c < e (1)
			
			# valid helper calls because b, e are in naturals by Pre and c 
			# in naturals as c = floor(2*b + e + 2) and 0 <= b < c < e < len(L)
			
			# valid recursively as,
			# size(b, c) = c - b < e - b = size(b, e) because c < e by (1)
			# size(c, e) = e - c < e - b = size(b, e) because c > b by (1)
			return helper(b, c) + helper(c, e)
			# valid return because by RH, helpers return a list of elements in
			# each slice that are less than x s.t. L = L[b:c] + L[c:e]
			
	# valid len(L) because L is a list by Pre
	
	# valid call because 0 in naturals and len(L) in naturals by pre
	# furthermore, len(L) >= 0 is vacuously true.
	return helper(0, len(L))
	# correct return because helper returns number of elements less than x
	# between L[0:len(L)]
```