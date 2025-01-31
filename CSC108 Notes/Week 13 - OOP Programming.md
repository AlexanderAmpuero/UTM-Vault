#csc108
Object-Oriented Programming is an approach to develop code
```python
class Point:
	def __init__(self): # initializer, applys to everything
	self.x = 0
	self.y = 0
```
- An object references itself using *self*

###### Instantiation - When memory is allocated to an object
###### Initialization - When values are put into the memory and allocated to an object
###### Declaration - When you tell the program that there will be an object of a specific type with a given name
```python
class Student:
	def __init__(self, name: str, gpa: float, sid: int, credits: float) -> None:
		self.name = name
		self.CGPA = gpa
		self.ID = sid
		self.credits = 0

	def passing(students: list[Student], grade:int) -> list[Students]:
		'''Return a list of Students with CGPA >= grade.'''
		stu = []
		for student in Student:
			if self.CGPA >= grade:
				stu.append(grade)
		return stu

class Course():
	def __init__(self):
		self._classlist = []
		self._grades = {}

	def add_student(self, student: Student) -> None:
		self._classlist.append(student)

	def remove_student(self, student_id: int) -> None:
		self._classlist = [student for student in self._classlist if not                                    student.is_student(ID)]

	def add_grade(self, name: str, marks: dict[student, int]) -> None:
		self._grades.selfdefault(name, {}).update(marks) # creates a dictionary
														# then updates
			
```

##### Methods
```python
str.lower()
str.split()
str.strip()

class Person:
	def __init__(self, name): #initialize object
		self.name = name

	def introduce(self):
		print(f"Hi my name is {self.name}")
		

```

If someone says "self._value" instead of "self.value". then the "_value" should not be changed

If a class function has a return value, then when ".function()" is called in your code you with get a value, otherwise if it returns none, it mutates the variable in the background

Single underscore means a private function. double underscore is a build in python function

```python
class Firework:
    def __init__(self, timer: int) -> None:
        self._timer = timer
        self._storage = 0

    def update_timer(self, adder: int) -> str:
        """Add to storage and check if the firework should explode."""
        self._storage += adder
        if self._storage >= self._timer:
            return "*Ka Boom*"

    def __str__(self) -> str:
        """Return the string representation of the firework."""
        return "Still a firework."

	def __repr__(self) -> str: # for when you try to print "b"
		return "Still a firework." 
```

```python
class Course:
	def __init__(self):
		self._classlist = []
		self._grades = {}

	def add_student(self, student: Student) -> None:
		self._classlist.append(student)

	def remove_student(self, student_id: int) -> None:
		
```

 # Learning Methods
 ```python
class fractions:
	def __init__(self, x: int, y: int) -> None:
		self.x = x
		self.y = y

	def __add__(self, other: Fraction) -> int:

	def __str__(self) -> str:
		return f"The Fraction is {self.x} / {self.y}"
	def __repr__(self) -> str:
		return f"{self.x} / {self.y}"
		
 ```

```python
class Characters:
	def __init__(self) -> None:
		self.dick = {}

	def add(self, char: str, game: str) -> dict:
		return self.dict[char] = game
```
