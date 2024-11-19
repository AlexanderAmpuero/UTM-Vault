##### Lazy Literature
Codify the following information
```python
#Rutwa played soccer.
#Rutwa walked fast.
#Rutwa said so.

{'Rutwa' : ['played', 'walked', 'said']
'played' : 'soccer.'
'walked' : 'fast.'
'said' : 'so.'}
```

- Now we make a function to make dictionaries
```python
>>> def make_dictionary(filename: str) -> dict[str, list[str]]:
>>> 	'''step 1: empty string
>>> 	   step 2: Open the file
>>> 	   step 3: how do you add the key and value pairings correctly?'''
>>> 	word_d = {}
>>> 	with open(filename) as f:
>>> 		words = f.read().split() # puts each word into a list
>>> 		prev_word = ''
>>> 		for word in words:
>>> 			word_d.setdefault(prev_word, []).append(word)
>>> 			prev_word = word
>>> 		return word_d
```

```python
def mimic_text(word_dict: dict[str, list[str]], num_words: int) -> str:
	#step 1: read file
	#step 2: make dictionary (codify)
	#step 3: mimic text

	curr_word = ""
	words = []

	for i in range(num_words):
		possible_words = word_dict[curr_word]
		curr_word = choice(possible_word)
		words.append(curr_word)
	return ' '.join(words)
```

- Dictionaries cannot be keyed by lists

- Task 1: update your lazy literature code from the previous class to use 3-gram keys instead. Generate the text

```python
>>> from random import choice, randint
>>> 
>>> def random_element(lst):
>>> 	return lst[randint(0,len(lst) - 1)]
>>> 	
>>> def associate_pair() # keys are tuples, values are word
>>> 
>>> def make_dictionary(f: TextIO) -> dict[tuple[str], list[str]]:
>>> 	""" return a dictionary where the keys are tuple of length 3 containing          words in <f> and the value for a key is the list of words that were              found to follow the key"""
>>> 	# step 1: initialization
>>> 	d = {}
>>> 	context = ('', '', '')
>>> 	
>>> 	# step 2: add the associate pairings
>>> 	# what is the associate pairing?
>>> 	for line in f:
>>> 		word_list = line.split()
>>> 		for word in word_list:
>>> 			associate_pair(d, context, word)
>>> 			# How do you get the next pairing?
>>> 			context = context[1:] + (word,)
>>> 		return d
>>> 		
>>> def mimic_text(word_dict: dict[tuple[str], list[str]], num_words: int)->str:
>>> 	story = []
>>> 	context = random_element(list(word_dict.keys()))
>>> 	
>>> 	for i in range(num_words):
>>> 		if context not in word_dict: #start a new context
>>> 			context = random_element(list(word_dict.keys()))
>>> 		word = random_element(word_dict[context])
>>> 		
>>> 		context = context[1:] + (word,)
>>> 	return ' '.join(story)
```

- Testing the following function

```python
def pow(x: int, y: int) -> int:
	''' Return x**y given integers <x> and <y>. '''
	>>> pow(2,2)
	4
	>>> pow(-2,-3)
	-1/8

def abs(x: int) -> int:
	"""return the abs value of <x>"""
	>>> abs(-1)
	1
	>>> abs(2)
	2
	>>> abs(0)
	0
	
	if x < 0:
		return -x
	return x

def indices(s: str, substr: str) -> list[int]:
	''' return the indices in <s> at which non-overlapping copies of <substr>
	start. <substr> is non empty'''

	>>> indices('WOW Coool pool look!', 'oo')
	[5, 11, 16]
	>>> indices('oooooo', 'oo')
	[0, 2, 4]

	i = 0
	indices = []
	while i < len(s):
		if s[i: i + len(substr)] == substr:
			indices.append(i)
		else:
		 i += 1
	
	return indices

```

- generally how to make a dictionary with this crap 
```python 
story_list = ['And', 'the', 'fan,', 'and', 'the', 'cup,', 'And', 'the', 'ship,', 'and', 'the', 'fish.']
context_to_next_words = {}
for i in range(len(story_list)-2):
    context = (story_list[i], story_list[i+1])
    next_word = story_list[i+2]
    
    if context not in context_to_next_words:
        context_to_next_words[context] = []
    context_to_next_words[context].append(next_word)
```

##### Unit Testing
```python
import unittest
def test_is_simple() -> None:  
    input_list = [5,1,2,1,6] # 1 setup
    insert_after(input_list, 1, 99) # 2 call function
    expected = [5,1,99,2,1,99,6] # 3 assert expected value
```

- Unit Testing is intended to isolate components of a program and demonstrate that they are correct
- its purpose is to validate each unit of the software separately, ensuring it performs as designed
- Con: it may not catch issues when different pieces of code interact, like external modules.

```python
### IMPORTANT TEST SHIT
assertEqual(a,b) # checks a == b
assertNotEqual(a,b)
assertTrue(a) # bool(a) is True
assertFalse(a)
assertIs(a,b) # a is b
assertIsNot(a,b)
assertNone(a) # a is none
assertNotNone(a)
assertIn(a,b) # a in b
assertNotIn(a,b)
assertIsInstance(a,b) # isinstance(a,b)
assertNotIsInstance(a,b)
```

```python
import unittest  
import W09B  
MSG = "Function: {}, input: {}, expected: {}, received: {}."

class TestAbs(unittest.TestCase):
	""" The unit tests for w09b's Absolute Value Function (abs)"""
	def test_01_positive(self):
		""" Tests a positive value input"""
		result = W09B.abs(test_value)
		self.assertEqual(expected, result,
							MSG.format("abs()", test_value, expected, result))

	def test_02_negative_case(self):
		""" tests a negative value input. """
		test_value, expected = -2, 2
		result = W09B.abs(test_value)
		self.assertEqual(expected, result,
		MSG.format("abs()", test_value, expected, result))

```

- Example of a good Unit test
```python

# file is called running.py, the function return the key with the most elements
class TestMostPopular(unittest.TestCase):

    def test_one_item(self) -> None:
        """Test most_popular with a dictionary of length 1."""   
        actual = running.most_popular({'hello': [1,2,3]})
        expected = ['hello']
        self.assertEqual(actual, expected)
    
    
	def test_mutation(self) -> None:
        """Confirm that most_popular does not mutate the dict it is given.
        """
        actual_list = {'hello': [1,2,3]}
        expected = []
        actual_return = running.most_popular(actual_list)
        expected_return = ['hello']
        self.assertEqual(actual_list, expected)
        self.assertEqual(actual_return, expected_return)
```