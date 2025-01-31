#csc108
```python
import re
txt = "Today's tpopic in CSC108: Regexes"
x = re.search("CSC108", txt)
x = re.findall("416=[0-9]{3}-[0-9]{4}", txt)
print(x)
["416-358-3753", "416-356-1723"]
```

##### Regex
- Iterate through the input, looking for a match to the first char of the regex
- If there was a match...
	1. and the regex has no next char, and the string to the result. Advance to the next char and reset regex
	2. and the regex has another char, advance to the next input char and the next char in the regex. Repeat step.
- If it did not match, advance to the next input char and reset the regex to its start

```python
import re
txt = "csccsccsc"
re.findall(r'(csc)', txt)
['csc', 'csc', 'csc']
re.findall('(?:csc){3}', txt)
['csccsccsc']

txt = '''Mike Miller, Mick Furrier, Mike Baker, Mike Mason'''
re.findall("Mike \w*", txt)
['Mike Miller', 'Mike Baker']
```

```python
# now I try THIS BITCH IS GOING TO BE ON TT2 😞
def find_specific(txt: str) -> list[str]:  
	"""
	Return a list of matches where the string  
	begins with one 'a' followed by one or more 'b'  
 	>>> find_specific("aaabbbabbacb")  
	['abbb', 'abb']  
 	>>> find_specific("")  
	[]  
	"""
	
	return re.findall(r'[a]b+', txt) # I DID ITT
```

![[Pasted image 20241113114223.png]]
```python
### Task 2 ###  
def run_me(txt: str, my_regex: str) -> list[str]: # actual function 
	if len(my_regex) <= 6:  
		return re.findall(my_regex, txt)  
	return ["Fail"]  
def task2_helper(txt: str):  #helper function
	""" Create a regex that is no more than 6 characters long  
		that matches when a string, or substring, starts with  
		an 'a' followed by 0 or more numerals and ends  
		with the following string: 'CS'  
>>> 	task2_helper("a998289CSC is great aaCS")  
			['a998289CS', 'aCS']  
>>> 	task2_helper("aCSC108")  
			['aCS']  
>>> 	task2_helper("")  
			[]  
	"""  
	your_regex = r'a[1-9]+CS | a[CS]'  
	return run_me(txt, your_regex)
```

##### Intro To Sorting
