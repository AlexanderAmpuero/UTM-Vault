### 1. Complete the proof that algorithm `factored` is correct
```python
# Pre(n): n in Naturals
# Return b s.t. Post(b, n): b in B iff n is prime
def prime(n): # ... implementation omitted ...

# Pre(n): n in Naturals and n >= 2 and not prime(n)
# Return d s.t. Post(d, n): d in naturals and 2 <= d < n and d | n
def a_factor(n): # ... implementation omitted ...

# Pre(n): n in Naturals and n >= 2
# Return a0, ..., am are prime and n = a0 * ... * am
def factored(n):
	# let size(n) = n in naturals (by Pre) therefore valid measure
	
	# Assume RH
	
	# Valid Preprime(n) and valid if, because n in naturals and prime(n) returns
	# boolean by Post
	if prime(n):
		return (n,)
		# valid return value because (n,) is a sequence of one prime 
		# (by its branch)
	else:
		# (C) not prime(n)
		
		# valid call because n >= 2 and n in naturals by Pre, and n is 
		# not a prime number by (C)
		d = a_factor(n)
		# therefore d in naturals+ and d|n =>  n//d = n/d
		
		# valid call: d in naturals and d >= 2
		# valid recursively: size(d) = d < n = size(n)
		
		as = factored(d)
		
		# By RH, let (a0, ..., am) = as be primes witnessing Post(as, d)
		# s.t. d = (a0, ..., am)
		
		# valid call: n/d in integers because d | n, and n/d > n/n = 1 
		# (because d < n) therefore n/d in naturals and n/d >= 2
		# Note: valid call because n in naturals by Pre and d|n => (n//d => n/d)
		
		# valid recursively
		# size(n // d) = n/d <= n/2 (because post a_factor d >= 2)
		# thus n/d <= n/2 < n = size(n)
		
		bs = factored(n // d)
		
		# By RH, let (b0, ..., bl) = bs be primes witnessing Post(bs, n//d)
		# s.t. n//d => n/d = (b0, ..., bl)
		
		# valid + operator as it concatonates 2 tuples
		return as + bs
		# valid return value as (a0, ..., am) and (b0, ..., bl) are all primes
		# n = d * n/d (d from as and n/d from bs) = (a0, ..., am) * (b0, ..., bl)
```
##### Checklist for future proofs
1. Assume RH when beginning function
2. Prove base case is valid - that is, both operator and used methods. Show it has correct return values and that the function call is valid (not violating functions Pre())
3. Repeat step 1/2 for any function call or operation used. Show valid Post() and Pre()

### 2. Complete the proof the the following algorithm is correct
```python
# Pre(n, m): n,m in naturals and n < 2**m
# Return b s.t. Post(b,n,m): b is a tuple of 
# length m, and n = sum(b[i]*2^i) for i = 0 to m-1
def rep(n, m):
	# let size(n,m) = m in naturals (by pre) implying valid measure
	# Assume RH
	
	# valid call and valid == operator becasue m in naturals. == operator
	# produces boolean
	if m == 0:
		return ()
		# valid return because () is a tuple of length m (0 by if) and 
		# n = sum(b[i]*2^i) for i = 0 to m-1, since m = 0, n is arbitrary
		# n < 2^m = 2^0 = 1, thus n < 1 and n in naturals imply n = 9 by Pre
		
	else:
		# (C) m >= 1 because m in naturals (by pre) and m != 0
		
		# valid %, //, -, because n, m in naturals by pre
		
		# By RH: rep(n//2, m-1) is a length m-1 tuple b with
		# n//2 = sum(b[i]*2^i) for i = 0 to m-2
		
		# valid + operator because it concatonates a tuple with tuple b
		
		# valid rep call
		# m in naturals (by pre) and m >= 1 (by C) so m - 1 in naturals
		# n in naturals by pre so n//2 in naturals
		# and n//2 <= n/2 < 2^m/2 = 2^(m-1)
		
		# valid recursively: size(n//2, m-1) = m-1 < m = size(n,m)
		return (n%2,) + rep(n//2, m - 1)
		# valid return because let b' be the return value, which is (n%2,) + b
		# then b' is a tuple of length 1 + (m-1) = m
		# and b'[0]*2^0 + ... + b'[m-1]*2^(m-1)
		# = (n%2)*2^0 + b[0]*2^1 + b[1]*2^2 + ... + b[m-2]*2^(m-1)
		# = (n%2) + 2(*b[0]*2^0 + b[1]*2^1 + ... + b[m-2]*2^(m-2))
		# = (n%2) + 2(n//2) (quotient-remainder decomposition of n) = n
		
print(rep(2, 8))
```

### 3. Prove that the following algorithm is correct
Assume all arithmetic operations are exact (for real numbers, not floating point)
The algorithm doesn't have a valid recursive structure, since its call tree can be infinite. But the post condition only states that *if* it returns, then the return value has a certain property. So to prove it correct you don't need to (and can't) show that recursive calls are valid recursively, so don't even need to (and can't) define a measure of input size.

Still, show that all non-recursive calls are valid so that their return values can be assumed to satisfy their post conditions. That proves something a bit stronger, which we've added to the post condition but will always be there implicitly: those calls aren't a reason the function doesn't return

The recursive calls still need to be valid s.t. you may assume RH (if/when the recursive calls return). Since the only precondition is that the function is called with no arguements: you can point out once at the beginning of the function body that all the recursive calls are valid for that reason

```python
# [Non deterministically] return b s.t. Post(b): b in boolean
def maybe(): # ... implementation omitted ...

# [Implicit precondition from the def form: called with no arguements]
# Doesn't raise an exception, and]
# If r is returned, then post(r): there exists k in integers, s.t.,
# r = 3^k

def V():
	# NOTE: all recursive calls are valid since no arguments
	# RH: if r is returned, then r = 3^k for k in integers
	
	# valid if operator because maybe returns boolean
	if maybe(): return 3
	# valid return because 3 = 3^1 for k = 1
	
	# valid elif operator because maybe() is nondeterministic 
	# and may returns boolean
	elif maybe(): return V()^2
	# valid return because by RH, if V() returns r for r = 3^k
	# r^2 = (3^k)^2 => r^2 = 3^2k and 2k is in k as needed
	
	# valid else operator because maybe() is nondeterministic 
	# and may returns boolean
	else: return V() / 3 # exact division, no rounding
	# valid return because by RH, if V() returns, then r = 3^k
	# we are returning V()/3 => r/3 = (3^k)/3 =>
	# r/3 = 3^(k-1) and k-1 in integers as needed
```

### 4. Prove that the following algorithm is correct
```python
# [Non deterministically] return b s.t. Post(b): b in boolean
def maybe(): # ... implementation omitted ...

# If s is returned, then Post(s):
	# s is a non-empty string, and
	# the firsr char of s is a variable ('x,'y','z'), and the
	# last character of s is a variable ('x','y','z'), and
	# the number of occurences of operators ('-','*') in s is one
	# less than the number of occurrences of variables
	# ('x,'y','z') in s
	
def E():
	# All recursive calls are valid because no arguments
	
	# RH: If s is returned, s is a sequence of chars in ('x,'y','z')
	# s.t. s is a non empty str where its first char is in ('x,'y','z')
	# and its last char is in ('x,'y','z')
	# furthermore, the number of occurences of ('-','*') is one less
	# than the occurences of ('x','y','z') in s.


	# all maybe() calls are valid for either if or elif, 
	# because maybe() is nondeterministic, and always returns a boolean
	if maybe(): return 'x'
	# valid return because variable occurence is 1, where 'x' is in ('x,'y','z')
	# and the string both starts and ends with char 'x'. Lastly, ('-','*') 
	# occurences are 0, satisfying the RH.
	elif maybe(): return 'y'
	# valid return because variable occurence is 1, where 'y' is in ('x,'y','z')
	# and the string both starts and ends with char 'y'. Lastly, ('-','*') 
	# occurences are 0, satisfying the RH.
	elif maybe(): return 'z'
	# valid return because variable occurence is 1, where 'z' is in ('x,'y','z')
	# and the string both starts and ends with char 'z'. Lastly, ('-','*') 
	# occurences are 0, satisfying the RH.
	
	# '*' operator is valid because it is concatinating to strings.
	elif maybe(): return E() + '*' + E()
	# We will prove that 'E() + '*' + E()' satisfies the RH, but first we must
	# establish definitions within the RH. Let the number of occurences of 
	# ('x,'y','z') = n0 for some n in naturals, and the number of occurences of 
	# ('-','*') = n0-1, as by definition, ('-','*') occurences is one less within
	# the string s.
	# By the definition of E(), (E() + E()), let the first call of E() = n1
	# and the second call E() = n2 s.t.
	
	# contains (n1+n2) occurences of ('x,'y','z')
	# Thus, there are (n1+n2) - 2 occurences of ('-','*').
	# Furthermore, we are using '*' operator to concatinate the two strings, 
	# thus we have (n1+n2) - 2 + 1, or (n1+n2)-1 occurences of ('-','*').
	# Hence, we have one less occurence of ('-','*') in s where 
	# s = E() + '*' + E().
	# 2. > Since the first character of the left recursive result is a variable,
	#  and the last character of the right recursive result is a 
	# variable, the concatenated string begins and ends with a variable. and 
	# above we have proven that the number of occurences of ('-','*') is 
	# one less than the occurences of ('x','y','z') in s.
	
	# valid '-' operator, as it is concatinating two seperate strings
	else: return E() + '-' + E()
	# This return value is valid for the exact same reason as the elif, thus
	# the proof has been ommited. 
```
