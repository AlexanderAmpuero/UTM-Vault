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

![[Pasted image 20250915145704.png]]
```python
# Pre(n): n in naturals and n >= 2
# Return r s.t. Post(r, n): r = aa(n) (from the previous problem)
def ab(n):
	# let size(n) = n
	# valid measure as n is in naturals by Pre
	if type(n) is not int: raise TypeError()
	elif n < 2: raise ValueError()
	# add the smallest number of base cases here
	if n < 4: return aa(n) # Corrected Ver.
	
	
	else:
		# (C): n >= 4, as n is in naturals and n >= 2 by Pre, and n > 4 by
		# base cases
		# Valid call: It is valid because n >= 4 => sqrt(n) >= 2
		# => n > sqrt(n) >= 2 (by sqrt(x) < x)
		# => n > floor(sqrt(n)) >= 2 (floor does nothing to number in naturals)
		# Valid recursively: size(floor(sqrt(n))) = floor(sqrt(n)) < n = size(n)
		# Thus since n in naturals, n is decreasing with a minimum value.
		r = ab(floor(sqrt(n)))
		return r ** 2 + 2 * r
```
Did too much - for valid call didn't need `n > sqrt(n)`, just needed to show Pre(n) is satisfied

![[Pasted image 20250915153440.png]]
```python
# Pre(n): n in Naturals
# Return r s.t. Post(r, n) r in Naturals and ...
def s(n):
	# let size(n) = n
	# Valid measure because n in naturals by Pre
	r = n
	for k in range(n):
		# (C) k in naturals and k < n
		# Valid call: k is in the naturals (by C)
		# Thus k is a valid call

		# Valid recursively: valid because size(k) = k < n = size(n) (by C)
		
		r = r + s(k)
	return r
```

Valid Recursively because the range function defines k as {k: 0 <= k <= n - 1} via for loop

![[Pasted image 20250915154429.png]]

![[Pasted image 20250915160303.png]]
```python
# Pre(n): n in naturals
# Return b s.t. Post(b, n): b in B and b iff n is prime
def prime(n): # ...implementation omitted...

# Pre(n): n in naturals and n >= 2 and not prime(n)
# Nondeterministically return d s.t. Post(d, n): d in naturals and
# 2 <= d < n and d divides n. 
def a_factor(n): # ...implementation omitted...

# Pre(n): n in naturals and n >= 2
# Return a0, ..., am s.t. Post(a0, ..., am, n): m in naturals and a0, ..., am
# are prime and n = a0 x ... x am
def factored(n):
	# let size(n) = n
	# valid measure as n in naturals by Pre
	if prime(n): return (n,)
	# (C) n > 3 and n is not prime
	
	# Valid Call: By Post of a_factor(), 2 <= d < n and d divides n,
	# thus by definition, n is not prime, and n != 3 (as if d = 2, then 2 !| 3)
	# Thus (C) is satisfied as needed.
	
	# Valid Recursively: By Post of a_factor(), 2 <= d < n. Furthermore,
	# size(d) = d < n = size(n) by Post of a_factor(), and 
	# size(n // d) = (n // d) < n = size(n) (because d | n)
	d = a_factor(n)
	as = factored(d)
	bs = factored(n // d)
	return as + bs
```
Find `factored(12)`
`factored(12)` = `factored(a_factor(12))` + `factored(n // a_factor(12))` = (3, 2, 2)
		`factored(a_factor(12))` = (3,)
				`a_factor(12)` = 3
		`factored(12 // a_factor(12))` = `factored(4)`
				`a_factor(12)` = 3
				`factored(4)` = `factored(a_factor(4))` + `factored(n // a_factor(4))` = (2, 2)
						`factored(a_factor(4))` = (2,)
								`a_factor(4)` = 2
						`factored(n // a_factor(4))` = (2,)
								`a_factor(4)` = 2
or
`factored(12)` = `factored(a_factor(12))` + `factored(n // a_factor(12))` = (2, 3, 2)
		`factored(a_factor(12))` = (2,)
				`a_factor(12)` = 2
		`factored(12 // a_factor(12))` = `factored(6)` = (3, 2)
				`a_factor(12)` = 2
				`factored(6)` = `factored(a_factor(6))` + `factored(n // a_factor(12)` = (3, 2)
						`factored(a_factor(6))` = (3,)
								`a_factor(6)` = 3
						`factored(6 // a_factor(6))` = (2,)
								`a_factor(6)` = 3

Did too much for the call tree, could've simplified it and didn't need to show `a_factor()` calls. 
Need to show valid calls and recursion for each function call in the recursive step.
That is, `a_factor()` and `factored()`

![[Pasted image 20250915232429.png]]
```python
# Pre(n, m): n, m in naturals and n < 2**m
# Return b s.t. Post(b, n, m): b is atuple of length m, and n *above*
def r(n, m):
	# size(n, m) = m
	# valid measure as n, m in naturals by Pre
	
	if m == 0: return ()
	# (C) m is in the naturals and m >= 1 (by Pre)
	
	# Valid call: By (C), we know that m - 1 >= 0, 
	# thus m - 1 >= 0 => (m - 1) in naturals. Furthermore, 
	# n < 2**m => n/2 < 2**m * 1/2 => floor(n/2) < 2**(m - 1). 
	# Thus since m1 = m0 - 1, the precondition is satisfied
	
	# Valid recursively: size(n // 2, m - 1) = m - 1 < m = size(n, m) by (C)
	return (n % 2,) + r(n // 2, m - 1)
```
Proof: We will use induction to prove size(n, m) = m
Base Case: let m = 0. By Pre, we know n < 2^m => n < 2^0 => n < 1, thus n = 0.
r(0, 0) = (). Thus size(n, m) = m as needed

Recursive Hypothesis: size(n // 2, m - 1) = m - 1 for some m, n in naturals s.t. m > 0

Recursive Step: n // 2 < 2^(m - 1)
=> n // 2 <= n / 2 < 2^(m - 1)
=> n < 2 * 2^(m - 1)
=> n < 2^m 
=> Thus size(n // 2, m - 1) = m - 1 => size(n, m) = m
Hence, by the principle of mathematical induction, size(n, m) = m for all m in Precondition
Note: measure decrease proven in the above code

![[Pasted image 20250916002901.png]]
```python
# Pre(a, b): a, b in naturals and (a > 0 or b > 0)
# Nondeterministically return (c, d) s.t. Post(c, d, a, b): c, d in naturals and
# either c = a and d < b, c < a and d = b
def adversary(a, b): # ... implementation omitted...

# Pre(m, n): m, n in naturals and m != n
# return b s.t. Post(b, m, n): b in B and (...omitted)
def strategy(m, n):
	# size(m, n) = m x n or size(m, n) = max(m, n) are both valid measures 
	# valid measure as m x n and max(m, n) is in naturals by Pre
	if m == 0 or n == 0: return True
	else:
		# (C) m >= 1 and n >= 1 and m != n (by Pre)
		
		(c, d) = adversary(min(m, n), min(m, n))
		# Valid Call: Let x = min(m, n), s.t. 
		# adversary(min(m, n), min(m, n)) = adversary(x, x)
		# We know x > 0 by (C) and x in naturals by Pre. Thus Pre of adversary
		# Is satisfied. 
		# by adversary(x, x) Post, 
		# we know if a = b, then either (c = a and d < b) or (c < a and d = b)
		# Thus c != d, and c, d in naturals by Post of adversary.
		# Thus Pre strategy is satisfied as needed
		
		# NOTE: proofs for size below.
		# Valid Recursively: 
		# size(c, d) = c x d < m x n = size(m, n)
			# Because adversary Post = (c, d, min(m, n), min(m, n)) where either
			# c = min(m, n) and d = b or c < min(m, n) and d = b.
			# Thus size(c, d) =c x d < min(m, n) * min(m, n) < m x n = size(m, n)
		# size(c, d) = max(c, d) < max(m, n) = size(m, n)
			# Because adversary post = (c, d, min(m, n), min(m, n)) implying
			# c = min(m, n) and d < min(m, n) or d = min(m, n) and c < min(m, n)
			# either event results in max(c, d) = min(m, n) < max(m, n)
			# since m != n by Pre. 
		return strategy(c, d)
```

a) size(m, n) = m
Let (m, n) = (2, 5)
strategy(m, n) = 
	adversary(2, 2)
	strategy(2, 1)
Thus size(m, n) != m, as the size must decrease on every call, but size remains to be 2
b) size(m, n) = m - n
Not valid as let (m, n) = (2, 3) => size(m, n) = -1 which is not a valid measure

Both size(m, n) = max(m, n) and size(m, n) = m x n are valid - Shown above


