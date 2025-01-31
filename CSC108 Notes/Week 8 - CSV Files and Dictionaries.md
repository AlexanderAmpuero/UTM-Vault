#csc108
```textfile
A0,A1,A2,A3
B0,B1,B2,B3
```

- Using a text file as a dictionary
```python
>>> line.split(",")
['A', 'B', 'C']
>>> with open("data.csv", "r") as csv_file
>>> 	for line in csv_file:
>>> 		for value in line.split(",")
>>> 			print(value)
A0
A1
A2
A3

B0
B1
B2
B3
```

- Some csv files contain headers - human readable string describing the data in the column
```textfile
FirstName,Lastname,Role
Alice,Liddel,Teaching Assistant
Bob,Dylan,Instructor
Carol,Lewis,Student
```
- The header can be skipped by...

```python
>>> with open("data.csv",'r') as csv_file:
>>> 	csv_file.readline()
>>> 	for line in csv_file:
>>> 		for value in line.split(",")
```
- Trying it myself (fixed by Rutwa)
```python
def highest_rated_band(file_name: str) -> str:
	with open("file_name",'r') as f:
		f.readline() # read header
		max_rating = -float('inf')
		ans = ""
		for line in f:
			line = line.split(",")
			band_name = line[0]
			rating = int(line[1])

			if rating > max_rating:
			ans = band_name
			max_rating = rating
	return ans
```
- (I had the right idea but checked every value, where prof checked each index)

- Try to right another function where we write into a csv
```python
>>> def lists_to_csv(xs: list, ys: list, zs: list, file_name: str) -> None:
>>> 	with open("file_name", "w") as f:
>>> 		for i in range(len(xs))
>>> 			f.write(f'{xs[i]}, {ys[i]}, {zs[i]}\n')
```

##### Hash Function
- In both cases we provided a way to map a key (in this case, a character) to a value (in this case, a number)
```python
>>> def hash_2(key: str, xs: list[int]) -> int:
>>> 	# one to one encoding
>>> 	pos_in_alphabet = ord(key) - ord('a')
>>> 	return xs[pos_in_alphabet]
```
- Gets the difference in ascii values and return the difference as a position in the alphabet

#### Dictionaries
- Note: indexing into a dictionary refers to the key, ex. xs['h']
```python
>>> xs = {'h': 1, 'e': 1, 'l': 3, 'o': 2, 'w': 1, 'r': 1, 'd': 1}
>>> a = {[1]: 'a', [2]:'b'} # This Cannot be a dictionary
```

- The definition of a dictionary in python is a key binded to a value

```python
>>> H = {"red": ["apple", "firetrucks", "cars"], "yellow": ["banana", "cars"]}
>>> H.items()
dict_items("red": ["apple", "firetrucks", "cars"], "yellow": ["banana", "cars"])
>>> H.values()
dict_values(["apple", "firetrucks", "cars"], ["banana", "cars"])
>>> H.keys()
dict_keys(['red', 'yellow'])

>>> "blue" in H
False

>>> H["red"]
["apple", "firetrucks", "cars"]
>>> H["blue"] = ["sky"] # we can do this, because Rutwa
```
- Now we have problems for squanch to complete

```python
def char_index(s: str) -> dict[str, list[int]]:
	"""return the indices of the character in <s>"""
	index_d = {}
	for (index, ch) in enumerate(s):
		if ch not in index_d:
			index_d[ch] = []
		index_d.append(index)
	return index_d
```

```python
def reverse_lookup(d: dict, item: any) -> list:  
    """ Return a list of all keys k such that d[k] == item"""  
    return_lst = []  
    for (key, value) in d.items():  
       if item == value:  
          return_lst.append(key)  
    return return_lst
```

```python
def combine(d1: dict[int, list[int]], d2: dict[int, list[int]]) -> dict[int,int]
	"""return the dictionary where each key is a key that is in both d1 and d2"""
	return_d = {}
	for key in d1:
		if key in d2:
			combined[key] = sum(d1[key]) + sum(d2[key])
	return combined
```

# Tuples and Nested Dictionaries
- A tuple is an ordered pair of variables similar to a string and a list, storing multiple items

Dictionary Warmup
```python
>>> def reverse_lookup(d: dict, item: any) -> list:  
>>> """ Return a list of all keys k such that d[k] == item"""  
>>> return_lst = []  
>>> for (key, value) in d.items():  
>>>    if item == value:  
>>>       return_lst.append(key)  
>>> return return_lst


>>> def invert(H: dict) -> dict[list]:
>>> 	invert = {}
>>> 	for value in H.values():
>>> 		inverted[value] = reverse_lookup(H, value)
>>> 	return invert
```

##### Nested Dictionaries
- As with lists, dictionaries can be nested
```python
>>> book_series = {'Animorphs': {'author': 'K.A. Applegate', 'number': 54, 'genre':  
'middle grade'},  
'Inheritance Games': {'author': 'Jennifer Lynn Barnes', 'number': 2,  
'genre': 'adult'},  
'Shopaholic': {'author': 'Sophie Kinsella', 'number': 10, 'genre':  
'adult'}}
>>> def find_books(book_series:dict[str, dict], genre: str) -> list[str]:
>>> 	new_list = []
>>> 	for series in book_series:
>>> 		if book_series[series]['genre'] == genre:
>>> 			new_list.append(series)
>>> 	return new_list

```

##### Trie as Dictionary
```python
>>> def update_trie(word: str, trie: dict[str, dict]) -> None:  
	"""  
	Update <trie> with <word>.  
>>> 	trie = {}  
>>> 	update_trie('this', trie)  
>>> 	trie  
	{'t': {'h': {'i': {'s': {}}}}}  
>>> 	update_trie('that', trie)  
>>> 	trie  
	{'t': {'h': {'i': {'s': {}}, 'a': {'t': {}}}}}  
	"""  
	while word != '':  
		w, word = word[0], word[1:]  
		w_dict = trie.setdefault(w, {})
```

- Now we try building this tree
```python 
def build_trie(words: list[str]) -> dict[str, dict]:  
	"""  
	Return the word trie for the input <words>.  
	"""  
	trie = {}  
	for word in words:  
		update_trie(word, trie)  
	return trie
```

- Checking if a word is found in trie
```python
>>> def in_trie(word: str, trie: dict[str, dict]) -> bool:  
	"""  
	Return True iff <word> is found in <trie>.  
>>> 	trie = build_trie(["that", "these", "those"])  
>>> 	in_trie('that', trie)  
	True  
>>> 	in_trie('paul', trie)  
	False  
	"""
		
>>> 	curr_dict = trie
>>> 	for ch in word:
>>> 		if ch not in curr_dict:
>>> 			return False
>>> 		curr_dict = curr_dict[ch]
>>> 	return True
```