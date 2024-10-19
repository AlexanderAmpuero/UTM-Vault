# File Manipulation
- Learn to open and close
- Learn to read files
- All text files
- Convenient way to record and use data
###### Definition of a File
A file is a collection of data in a persistent storage system
###### Definition of a Text File
A text file is a file that contains human-readable characters, today we learn to manipulate them in python

##### Reading FIles
```python
>>> file = open("hello.txt")
>>> file.readline()
'What a\n'
>>> file.readline()
'wonderful\n'
>>> file.readline()
'hello world.'
>>> file.readline()
''

# print every line in file
>>> for line in file:
>>> 	print(line)
What a

wonderful

hello world

>>> close(file)
```

##### Opening and Closing Files
```python
with open(filename, 'r') as f:
	print(readline(filename))

# Give the sum of all numbers inside a text file
def sum_file(filename: str) -> int:  
    '''  
    Return an integer containing the sum of all the numbers    '''    numbers = []  
    sum = 0  
    with open(filename, 'f') as f:  
        for line in f:  
            numbers.append(int(line))  
    for i in numbers:  
        sum += i  
    return sum  
  
print(sum_file("numbers.dat"))
6
```