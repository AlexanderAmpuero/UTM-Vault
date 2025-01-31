#csc108
# File Manipulation
- Learn to open and close
- Learn to read files
- All text files
- Convenient way to record and use data
###### Definition of a File
A file is a collection of data in a persistent storage system
###### Definition of a Text File
A text file is a file that contains human-readable characters, today we learn to manipulate them in python

##### Reading Files
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
    Return an integer containing the sum of all the numbers    '''    
    numbers = []  
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

###### How to Write into a txt
```python
from typing import TextIO

def write_ascii_triangle(triangle_doc: TextIO, block: str, 
                         sidelength: int) -> None:
    """Write an ascii isosceles right triangle of character block that is
    sidelength characters wide and high to triangle_doc. The right
    angle should be in the upper-left corner. For example, given
    block="@" and sidelength=4, the following should be written to triangle_doc:
    
    @@@@
    @@@
    @@
    @
    
    Precondition: len(block) == 1    
    """
    for i in range(sidelength):
        triangle_doc.write(block * sidelength + "\n")
        sidelength -= 1
```
