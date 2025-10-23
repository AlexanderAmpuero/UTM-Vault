# What is Inheritance?
All objects are related to one another, and beyond that, they each have something in common.
For example, bikes; you have mountain bikes, race bikes, tricycles, and more. They each *inherit* from the *abstract* class of **bike**.
Now although they all inherit from the *superclass* of **bike**, they each have their own unique features which either override, and add onto the existing features. 
In Java, each class can only have one *superclass*, which is implemented using the **extends** keyword.
# What is an Interface?
Objects define their interactions through the methods they share, that is, *public* methods. These methods form the interface that the outside world interacts with. For example, the buttons on your remote, or a flesh interface. You don't know how they work, but you know that your input yields the desired output. 
In it's most common form, an interface is a group of related methods with empty bodies. 
In the example of a bike:
```java
interface Bicycle {
    //  wheel revolutions per minute
    void changeCadence(int newValue);

    void changeGear(int newValue);

    void speedUp(int increment);

    void applyBrakes(int decrement);
}
```
To implement this interface we've created, the name of your class would change, to a brand for example, using the **implements** keyword
```java
public class ACMEBicycle implements Bicycle {

    int cadence = 0;
    int speed = 0;
    int gear = 1;

   // The compiler will now require that methods
   // changeCadence, changeGear, speedUp, and applyBrakes
   // all be implemented. Compilation will fail if those
   // methods are missing from this class.

    void changeCadence(int newValue) {
         cadence = newValue;
    }

    void changeGear(int newValue) {
         gear = newValue;
    }

    void speedUp(int increment) {
         speed = speed + increment;   
    }

    void applyBrakes(int decrement) {
         speed = speed - decrement;
    }

    void printStates() {
         System.out.println("cadence:" +
             cadence + " speed:" + 
             speed + " gear:" + gear);
    }
}
```

# Unit Testing
Why use Unit Testing?
- forces thought on inputs and outputs of code
- identifies location of problems should they arise
- TDD: Test driven development = write test cases first then code
- Motivating: Yes!! Green light!

## JUnit
Each method in a Unit Test can be labeled as such:
- @BeforeAll: do general set up
- @BeforeEach: do some setup/prep work e.g., open a file, open a network connection.
- @Test: run the test case.
- @AfterEach: clean up after the test finishes, e.g., closing the file, close the connection.
- @AfterAll: rarely used, but for cases that must be ran last
Within a Unit Test, you follow the pattern of: BTA, BTA, BTA, ... , not BTTTTTTTTA.
Or you can follow, Ba (Be T Ae) (Be T Ae) (Be T Ae) (Be T Ae) Aa

For a Unit Test, you use assertions, below is an example. 
```java
import static org.junit.Assert.*;

import org.junit.Test;

public class BalloonTest {
	
	@Test
	public void testInflate() {
		Balloon b1 = new Balloon();
		assertEquals("Initial state of balloon, amount", b1.getAmount(), 0);
		assertEquals("Initial state of balloon, isPopped", b1.isPopped(), false);

		b1.inflate(10);
		assertEquals("After inflate 10, not popping, amount", b1.getAmount(), 10);
		assertEquals("After inflate 10, not popping, isPopped", b1.isPopped(), false);

		assertTrue("After inflate 10, not popping, amount (range)", 5<=b1.getAmount() && b1.getAmount()<=20);

		b1.inflate(100);
		assertEquals("After inflate further 100, popping, amount", b1.getAmount(), 0);
		assertEquals("After inflate further 100, popping, isPopped", b1.isPopped(), true);

		// fail("This always fails");
	}
}
```

# Unified Modeling Language (UML)
- Unified Modeling Language (UML) allows us to express the design of a program before writing any code.
- It is not limited to any language
- An extremely expressive language.
- We will use only a small part of the language, Class Diagrams, to represent basic OO design.
![[Pasted image 20251023014803.png]]
**Visibility**
Each method within a class is given a visibility marker:
`+ public`
`- private`
`# protected`
`~ package`
(the above class is entirely public methods, with private instance variables)

**Types of Classes**
```
Regular class: A
	Interface: <<interface>> A
	Abstract class: <<abstract>> A
```

**Static**: ___ - (underline)
**Arrows**:
![[Pasted image 20251023015352.png]]
An *association* is strong, it has classes interact with one another, but not alter anything. For example, `ClassA` stores a reference to `ClassB` as an attribute. Like this:
```java
public class ClassA {
    ClassB theClassB = ...
    ...
}
```
A *dependency* is weaker, it means that `ClassA` and `ClassB` interact and change one another, so if one fails, they both fail. For example:
```java
public class ClassA {
    ...
    public void someMethod(ClassB arg1) {...}
    ...
    public void someOtherMethod() {
        ClassB localReferenceToClassB = ...
    }
    ...
}
```
![[Pasted image 20251023020415.png]]
In this above example, the *Client* is **associated** with the *Publisher*, and **dependent** on a general *Subscriber* class, which connects them to the *Subscriber* **interface**. Beyond this, the *Publisher* is **associated** with the *Subscriber* **interface**, such that the *Publisher* may interact with the *Client* through the *Subscriber* **interface**.
**Note**: the diamond between the *Publisher* and *Subscriber* implies an aggregational relationship, which is similar to a compositional relationship.

- *Aggregation* = “has-a” relationship, but the contained objects (Subscribers) can exist independently.
- The *Publisher* “has” or “contains” a collection of **Subscribers**, but doesn’t own their lifecycle.

This means the *Publisher* has a collection of *Subscribers*, but they can exist without it.