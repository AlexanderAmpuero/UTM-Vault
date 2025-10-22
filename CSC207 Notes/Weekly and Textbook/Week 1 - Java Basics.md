### Size of Fundamental Types
```java
boolean bool = true; // N/A, can only be true or false
byte b = 1; // 8 bit
char c = 'a'; // 16 bit
short s = 2; // 16 bit
int i = 3; // 32 bit
long l = 4; // 64 bit
float f = 0.5; // 32 bit
double d = 6.6; // 64 bit
```
#### Data Types
Primitive Data Types are *immutable*, that is, when called in a program they are a direct access to the value of the variable. 
Non-primitive Data Types are generally *mutable* unless specified otherwise by the user, and they reference a memory address which then references Primitive Data Types(s)

All primitive data types have a default value, for example, ints have a default value of 0.
```java
public class Main {
	public static void main(String[] args) {
		int[] i_array;
		i_array = new int[10];
		System.out.println(i_array[0]);
	}
}
```
![[Pasted image 20251021182318.png]]
### Arrays
**Arrays** before they are initialized are primitive types, but once initialized they are arrays of a specified object. 
take `int[] i_array;` i_array is not initialized here, just being a primitive array object. 
`i_array = new int[10];` now we have initialized it as 10 integer objects within the array.
Thus i_array is a *data type* which stores *memory addresses* to various primitive integer objects.

Array - very basic data structure. It has a fixed, predetermined size that must be known at the time of creating (instantiating) the array. `.length'` is a property, not a method.

ArrayList - A concrete class implementing the List interface, used as seen below. Behaves like a Python list, increasing memory size as needed without a predetermined size.
```java
import java.util.List;
import java.util.ArrayList;


// creates an ArrayList of ints
List<Integer> myList = new ArrayList<>();

// adds 5 to myList
myList.add(5);

// prints the 0th element of myList, which is 5
System.out.println(myList.get(0));
```
Within `new ArrayList<>()` you can define the type within the array list, or you can leave it empty and ArrayList() defines the type itself by whatever is first added to it. 
#### Copying Arrays
`arraycopy()` is a built in Java method that can copy array data from one array to another.
The parameters of the method specify the starting position in the source array, the starting position in the destination array, and the number of array elements to copy.

```java
class ArrayCopyDemo {
    public static void main(String[] args) {
        String[] copyFrom = {
            "Affogato", "Americano", "Cappuccino", "Corretto", "Cortado",   
            "Doppio", "Espresso", "Frappucino", "Freddo", "Lungo", "Macchiato",      
            "Marocchino", "Ristretto" };
        
        String[] copyTo = new String[7];
        System.arraycopy(copyFrom, 2, copyTo, 0, 7);
        for (String coffee : copyTo) {
            System.out.print(coffee + " ");           
        }
    }
}
```
### Git
`git add`
`git commit`
`git push`

`git clone`
`git pull`
### Java
Just a text file, which contains Java code, ex. 
```java
public class HelloWorld {
	// the main method is exceuted
	public static void main(String [] args){ 
		System.out.println("hello world!");
	}
}
```
To run it, `javac HelloWorld.java` is executed, compiling the code so that computer understands it.
Then, run `java HelloWorld` to execute the code
#### Static v. Dynamic and Weakly v. Strongly Typed Languages
![[Pasted image 20251021154733.png]]
Java is a strong but statically typed strong language, meaning:
Statically - variables don't implicitly have a type
Strong - Values don't change in unexpected ways

### Unix
Used in Linux and osx
`cd` - enter
`ls` - list files
`git` - access git commands

## Variables
*Instance Variables* - A non-static field, that is, variables whose values are unique to their variable. ex. variable `speed` is unique to each object, a `bike` and `car` object have different speeds.
*Class Variables* - A static field, these have the `static` modifier when they are declared, which tells the compiler that there is one of these object in existence. Ex. `static int final age = 0` which can be used to say all humans start at `age = 0`, where the final operator implies this can never be changed. Or, `static int gears = 7` all bikes have 7 gears, but if you change it to `gears = 6` then this causes all bikes to have 6 gears.
*Local Variables* - Local variables are only usable by its local environment, that is, if a method crates `int count = 0` then another method cannot access this. The contrast to this is a *global variable* which everyone can access
*Parameters* - A parameter is always a variable, not a field, and it is used in methods to define "inputs", ex. `static void main(String[] args)` where args is the parameter, and String[] is the type of the args. 

## Naming
- Subsequent characters may be letters, digits, dollar signs, or underscore characters. Conventions (and common sense) apply to this rule as well. When choosing a name for your variables, use full words instead of cryptic abbreviations. 
- If the name you choose consists of only one word, spell that word in all lowercase letters. If it consists of more than one word, capitalize the first letter of each subsequent word. The names `gearRatio` and `currentGear` are prime examples of this convention. If your variable stores a constant value, such as `static final int NUM_GEARS = 6`, the convention changes slightly, capitalizing every letter and separating subsequent words with the underscore character. By convention, the underscore character is never used elsewhere.

## Operators
![[Pasted image 20251022000726.png]]
Most ideas translate from general programming knowledge, but a key point for me is the *ternary* operator, which is foreign to me, coming from python.
It is used as such,
```java
if (age > 18) {  
status = "adult";  
} else {  
status = "minor";  
}
// is the same as saying,
status = (age > 18) ? "adult" : "minor";
```
Furthermore, the precedence `instanceof` is a boolean comparison, and is used in class comparisons.
```java
class InstanceofDemo {
    public static void main(String[] args) {

        Parent obj1 = new Parent();
        Parent obj2 = new Child();

        System.out.println("obj1 instanceof Parent: "
            + (obj1 instanceof Parent));
    }
}
```
Which would return `true`, as the Child() object inherits from the Parent() 

## Switch
The switch is a useful Java statement, which acts like a simple if statement. 
```java
int month = 8;
if (month == 1) {
    System.out.println("January");
} else if (month == 2) {
    System.out.println("February");
}
...  // and so on
```
Or, more simply:
```java
public class SwitchDemoFallThrough {

    public static void main(String[] args) {
        java.util.ArrayList<String> futureMonths =
            new java.util.ArrayList<String>();

        int month = 8;

        switch (month) {
            case 1:  futureMonths.add("January");
            case 2:  futureMonths.add("February");
            case 3:  futureMonths.add("March");
            case 4:  futureMonths.add("April");
            case 5:  futureMonths.add("May");
            case 6:  futureMonths.add("June");
            case 7:  futureMonths.add("July");
            case 8:  futureMonths.add("August");
            case 9:  futureMonths.add("September");
            case 10: futureMonths.add("October");
            case 11: futureMonths.add("November");
            case 12: futureMonths.add("December");
                     break;
            default: break;
        }

        if (futureMonths.isEmpty()) {
            System.out.println("Invalid month number");
        } else {
            for (String monthName : futureMonths) {
               System.out.println(monthName);
            }
        }
    }
}
```

## While v. do-while
The while loop is simple, and thus explanation is omitted. 
The do-while loop is similar, but executes the `do` before the `while` is ever checked, causing `do` to be ran at least once.
```java
class WhileDemo {
	public static void main(String[] args){
		boolean flag = true;
		while (flag) {
			System.out.println("hi");
		}
	}
}
class DoWhileDemo {
    public static void main(String[] args){
        int count = 1;
        do {
            System.out.println("Count is: " + count);
            count++;
        } while (count < 11);
    }
}
```


