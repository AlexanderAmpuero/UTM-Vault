# Classes
We will use the below example to learn about classes:
```java
public class Bicycle {
        
    // the Bicycle class has
    // three _fields_
    public int cadence;
    public int gear;
    public int speed;
        
    // the Bicycle class has
    // one _constructor_
    public Bicycle(int startCadence, int startSpeed, int startGear) {
        gear = startGear;
        cadence = startCadence;
        speed = startSpeed;
    }
        
    // the Bicycle class has
    // four _methods_
    public void setCadence(int newValue) {
        cadence = newValue;
    }
        
    public void setGear(int newValue) {
        gear = newValue;
    }
        
    public void applyBrake(int decrement) {
        speed -= decrement;
    }
        
    public void speedUp(int increment) {
        speed += increment;
    }
        
}
```
Now we will create a sub class of the Bicycle parent class.
```java
public class RaceBike extends Bicycle {
	// private means we don't want users seeing, static means every bike
	// has the same weight, and final means the weight cannot be changed.
	private static final float weight = 15; // kg
	public float seatHeight; // inches
	
	public RaceBike(int startCadence, int startSpeed, int startGear, float seatHeight) {
		super(startCadence, startSpeed, startGear);
		seatHeight = seaHeight;
	}
	public void changeHeight(int newHeight) {
		seatHeight = newHeight;
	}
}
```
Where the RaceBike subclass inherits all 4 methods from Bicycle, and adds one of its own being the changeHeight().

Generally, classes are constructed in this order.
Class
	field
	constructor
	method(s)
and can be used the the `extends` to inherit something, and `implements` for when you are implementing an *interface*

## Overloading
You can overload a **method** or **constructor** by creating a new version below the existing version.
```java
public class DataArtist {
    ...
    public void draw(String s) {
        ...
    }
    public void draw(int i, double f) {
        ...
    }
}
```
for a method, or the following for a constructor:
```java
public class square {  
double width;  
double length;  
  
// Constructor with all dimensions  
public Box(double w, double l) {  
	width = w;  
	length = l;  
}  
  
// Constructor with no dimensions (default to 0)  
public Box() {  
	this(0, 0); // Calls the other constructor  
}  
  
// Constructor with only one dimension (for a cube)  
public Box(double side) {  
	this(side, side); // Calls the other constructor  
}
public static void main(String[] args) {  
	Box box1 = new Box(10, 20, 15); // Using the first constructor  
	Box box2 = new Box(); // Using the no-argument constructor  
	Box box3 = new Box(5); // Using the single-dimension constructor  
}
```
Overloading a constructor or method is special, but an unrecommended tactic because it makes your code less readable. But, it allows the `draw` method to take both `String` and `int, double` parameters, which is useful. 
## Passing Reference Data Type Arguments
Reference data type parameters, such as objects, are also passed into methods _by value_. This means that when the method returns, the passed-in reference still references the same object as before. _However_, the values of the object's fields _can_ be changed in the method, if they have the proper access level.

For example, consider a method in an arbitrary class that moves `Circle` objects:

```java
public void moveCircle(Circle circle, int deltaX, int deltaY) {
    // code to move origin of circle to x+deltaX, y+deltaY
    circle.setX(circle.getX() + deltaX);
    circle.setY(circle.getY() + deltaY);
        
    // code to assign a new reference to circle
    circle = new Circle(0, 0);
}
```

Let the method be invoked with these arguments:

`moveCircle(myCircle, 23, 56)`

When the method is called, `circle` parameter refers the the `myCircle` object, but the circle has its properties changed by the `setX` and `setY` method calls. Thus `circle` references a new `Circle` object, but myCircle still references the `circle`

# Objects
We will use the below code as groundwork for learning about **Objects**:
```java
public class CreateObjectDemo {

    public static void main(String[] args) {
		
        // Declare and create a point object and two rectangle objects.
        Point originOne = new Point(23, 94);
        Rectangle rectOne = new Rectangle(originOne, 100, 200);
        Rectangle rectTwo = new Rectangle(50, 100);
		
        // display rectOne's width, height, and area
        System.out.println("Width of rectOne: " + rectOne.width);
        System.out.println("Height of rectOne: " + rectOne.height);
        System.out.println("Area of rectOne: " + rectOne.getArea());
		
        // set rectTwo's position
        rectTwo.origin = originOne;
		
        // display rectTwo's position
        System.out.println("X Position of rectTwo: " + rectTwo.origin.x);
        System.out.println("Y Position of rectTwo: " + rectTwo.origin.y);
		
        // move rectTwo and display its new position
        rectTwo.move(40, 72);
        System.out.println("X Position of rectTwo: " + rectTwo.origin.x);
        System.out.println("Y Position of rectTwo: " + rectTwo.origin.y);
    }
}
```
A typical Java program creates many objects, which interact by invoking various methods. Through these object interactions, a program can carry out various tasks, such as implementing a GUI, running an animation, or sending and receiving information over a network. Once an object has completed the work for which it was created, its resources are recycled for use by other objects.

A class provides the blueprint for **Objects**, the following calls create 3 objects and assign them to variables.
**Point originOne** = new Point(23, 94);
**Rectangle rectOne** = new Rectangle(originOne, 100, 200);
**Rectangle rectTwo** = new Rectangle(50, 100);
Creating a Point, and two rectangle objects respectively.
Each of these statements contain 3 steps.
1. **Declaration**: The code set in **bold** are all variable declarations that associate a variable name with an object type.
2. **Instantiation**: The `new` keyword is a Java operator that creates the object.
3. **Initialization**: The `new` operator is followed by a call to a constructor, which initializes the new object.

### Declaring
By declaring a class object, you are assigning the object with an undetermined variable, storing a value. Then, we follow it with the `new` declaration, which instantiates it.
### Instantiation
The new operator instantiates a class by allocating memory for a new object and returning a reference to that memory. The new operator also invokes the object constructor.
The new operator requires a single call to the class, which allows it to be initialized
### Initializing
The initializer is the execution of the constructor, creating the object and allowing the instantiation to connect the declaration and the constructor. 
![[Pasted image 20251022013846.png]]
### The Garbage Collector (Extra Material)

Some object-oriented languages require that you keep track of all the objects you create and that you explicitly destroy them when they are no longer needed. Managing memory explicitly is tedious and error-prone. The Java platform allows you to create as many objects as you want (limited, of course, by what your system can handle), and you don't have to worry about destroying them. The Java runtime environment deletes objects when it determines that they are no longer being used. This process is called _garbage collection_.

An object is eligible for garbage collection when there are no more references to that object. References that are held in a variable are usually dropped when the variable goes out of scope. Or, you can explicitly drop an object reference by setting the variable to the special value null. Remember that a program can have multiple references to the same object; all references to an object must be dropped before the object is eligible for garbage collection.

The Java runtime environment has a garbage collector that periodically frees the memory used by objects that are no longer referenced. The garbage collector does its job automatically when it determines that the time is right.

# More on Classes
## Returning a Value from a Method
If you attempt to `return;` within a void method, you will receive a compiler error.
Any method not declared as void must have a `return` statement which matches the return type specified in the method header.

## Using the `this` Keyword
Within an instance method, `this` refers to the current object, that is, the object whose constructor or method is being called. You can refer to any member of the current object from within an instance method or a constructor by using `this`.
This is similar to Python, and the most common reason for using the `this` keyword is because a field is shadowed by a method or constructor parameter.
For example:
```java
public class Point {
    public int x = 0;
    public int y = 0;
    //constructor
    public Point(int a, int b) {
        x = a;
        y = b;
    }
}
// or it can be written as
public class Point {
    public int x = 0;
    public int y = 0;
    //constructor
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```
The difference being that the second`Point()` constructor uses parameters `x` and `y`, so to specify (and not cause a compiler error), we specify using `this` to say we want the class variable, and not the parameter. 

### Using `this` with a Constructor

From within a constructor, you can also use the `this` keyword to call another constructor in the same class. Doing so is called an _explicit constructor invocation_.
```java
public class Rectangle {
    private int x, y;
    private int width, height;
        
    public Rectangle() {
        this(0, 0, 1, 1);
    }
    public Rectangle(int width, int height) {
        this(0, 0, width, height);
    }
    public Rectangle(int x, int y, int width, int height) {
        this.x = x;
        this.y = y;
        this.width = width;
        this.height = height;
    }
    ...
}
```
Both of the top `Rectangle()` constructors use `this` to initialize some, or all of the Rectangle object's member variables. They provide default values for some constructor values, and have real world applications.
Note: If present, the invocation of another constructor must be the first line in the constructor.

## Controlling Access Members of a Class
Access level modifiers determine whether other classes can use a particular field or invoke a particular method. There are two levels of access control:
- At the top level - `public`
- At the member level - `public`, `private`, `protected`, or no modifier.
![[Pasted image 20251022015436.png]]
### Tips on Choosing an Access Level:
If other programmers use your class, you want to ensure that errors from misuse cannot happen. Access levels can help you do this.

- Use the most restrictive access level that makes sense for a particular member. Use `private` unless you have a good reason not to.
- Avoid `public` fields except for constants. (Many of the examples in the tutorial use public fields. This may help to illustrate some points concisely, but is not recommended for production code.) Public fields tend to link you to a particular implementation and limit your flexibility in changing your code.
- Rule of thumb: instance variables are private. Some methods are public.