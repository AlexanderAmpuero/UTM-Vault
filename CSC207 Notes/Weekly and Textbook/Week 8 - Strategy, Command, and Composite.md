# Strategy
A strategy consists of 2 types of classes. The *Strategy* and the *Context*. The *Context* determines how the *Strategy* is used. It allows for the Strategies to be swapped without altering the client that relies on it. 
![[Pasted image 20251216185855.png]]
### Reasons
Used for its ability to switch between implementations easily for different situations
### Examples
Imagine we have an interface for the behaviour of a robot. We have 3 behaviours made (strategies). These are, AggressiveBehaviour, SassyBehaviour, and FreakyBehaviour. We then have a Context class, which can use something like a for loop to cycle between each behaviour at a given time period. Thus hot swapping strategies. 
### Strategy Class
Interface
```java
package ca.utoronto.utm.designpatterns.strategy;

// generic strategy interface
public interface RobotStrategy {

	// returns a string which is the next command
	public String nextCommand();
}
```
Implemented
```java
package ca.utoronto.utm.designpatterns.strategy;

public class RobotStrategyAggressive implements RobotStrategy {

	@Override
	public String nextCommand() {
		return "attack";
	}
}
```

```java
package ca.utoronto.utm.designpatterns.strategy;

import java.util.Random;

public class RobotStrategyNormal implements RobotStrategy {

	private Random rand = new Random();

	@Override
	public String nextCommand() {
		int x = this.rand.nextInt(100);
		if (x < 60) {
			return "hold";
		} else if (x < 80) {
			return "attack";
		}
		return "defense";
	}
}
```
### Strategy Usage
Context class
```java
package ca.utoronto.utm.designpatterns.strategy;

public class Robot {

	private String name;
	private RobotStrategy strategy;

	public Robot(String name) {
		this.name = name;
		this.strategy = new RobotStrategyNormal();
	}

	void setStrategy(RobotStrategy strategy) {
		System.out.println("Setting strategy...");
		this.strategy = strategy;
	}

	void move() {
		System.out.print(name + " makes a move: ");
		String command = this.strategy.nextCommand();
		System.out.println(command);
	}

	public static void main(String[] args) {

		Robot robot = new Robot("AlphaGo");
		for (int i = 0; i < 10; ++i) {
			robot.move(); // these moves are normal
		}

		robot.setStrategy(new RobotStrategyAggressive());
		for (int i = 0; i < 10; ++i) {
			robot.move(); // these moves are aggressive
		}

		robot.setStrategy(new RobotStrategyDefensive());
		for (int i = 0; i < 10; ++i) {
			robot.move(); // these moves are defensive
		}
	}
}
```
# Command
A command is like a macro on your computer. Specified to do a series of tasks to accomplish a greater task. 
![[Pasted image 20251216191809.png]]
![[Pasted image 20251216192444.png]]
### Reasons
Capture a dynamic sequence of operations to carry out a task.
Want to create a sequence of commands executed in a precise order. The executor doesn't need to know anything about what the commands do or how they do it. 
Supports Undo/Redo functionality.
### Examples
A meal order. The waiter (Invoker), takes the order from the customer, which is then queued up to be cooked, and received by the cook (Receiver) who processes it. 
### Command Class
Abstract **Command**
```java
package ca.utoronto.utm.designpatterns.command;

public interface BalloonCommand {
	public abstract void execute(); 
}
```
Implementations:
```java
package ca.utoronto.utm.designpatterns.command;

public class InflateCommand implements BalloonCommand {
	private int amount=0;
	private Balloon balloon;
	public InflateCommand(int amount, Balloon balloon){
		this.balloon = balloon;
		this.amount = amount;
	}
	@Override
	public void execute() {
		balloon.inflate(amount);		
	}
}
```

```java
package ca.utoronto.utm.designpatterns.command;

public class DeflateCommand implements BalloonCommand {
	private int amount=0;
	private Balloon balloon;
	public DeflateCommand(int amount, Balloon balloon){
		this.balloon = balloon;
		this.amount = amount;
	}
	@Override
	public void execute() {
		balloon.deflate(amount);		
	}
}
```
### Command Usage
Client:
```java
package ca.utoronto.utm.designpatterns.command;

public class Client {
	public static void main(String [] args){
		Balloon b1 = new Balloon("red", 100);
		Balloon b2 = new Balloon("green", 200);

		BalloonCommandInvoker commands = new BalloonCommandInvoker();
		commands.add(new InflateCommand(10,b1));
		commands.add(new InflateCommand(10,b1));
		commands.add(new InflateCommand(30,b2));

		commands.executeAll();
		System.out.println(b1);
		System.out.println(b2);
		
		commands.executeAll();
		System.out.println(b1);
		System.out.println(b2);
		
		commands.executeAll();
		System.out.println(b1);
		System.out.println(b2);
	}

}
```
Invoker:
```java
package ca.utoronto.utm.designpatterns.command;

import java.util.ArrayList;

public class BalloonCommandInvoker {
	private ArrayList<BalloonCommand> commands = new ArrayList<BalloonCommand>();
	public void executeAll() {
		for(BalloonCommand bc:commands) {
			bc.execute();
		}
	}
	public void add(BalloonCommand command) {
		this.commands.add(command);
	}
}
```
# Composite
The intent of this pattern is to compose object into tree structures. 
![[Pasted image 20251216193431.png]]
A composite design pattern consist of the *Component*, *Leaf*, *Composite*, and *Client*.
*Component* - The AODigit. It is the abstract version of the leafs and composites. It defines the interface and must be implemented by its kids. 
*Leaf* - These are objects with no children.
*Composite* - A Composite stores child components in addition to implementing the *Component* interface. 
*Client* - The client is the car in this case, it manipulates the object using the component interface. 
### Reasons
Captures the idea of a list or tree of objects. No need to check for null.
### Examples
Graphics drawing editor. Imagine the following objects. Shapes, Lines, Rectangles, Complex Shapes, and the Graphics Editor. 
The Shapes would be the interface, and the Lines and Rectangles would be leafs of this implementation. Furthermore, Complex Shapes would be a composite of the Shapes interface. 
Lastly, we have the Graphics Editor being the client. 
### Composite Class
Interface
```java
package ca.utoronto.utm.designpatterns.composite;

public interface AODigit {
	public void increment();
	public String stringRep();
}

```
Composite
```java
package ca.utoronto.utm.designpatterns.composite;

public class ODigit implements AODigit {
	private AODigit next;
	private int digit;
	
	public ODigit(AODigit next) {
		this.next=next;
		this.digit=0;
	}

	@Override
	public void increment() {
		this.digit++;
		if(this.digit==9) {
			this.digit=0;
			this.next.increment();
		}
		
	}

	@Override
	public String stringRep() {
		return this.next.stringRep()+this.digit;
	}
}
```
Leaf
```java
package ca.utoronto.utm.designpatterns.composite;

public class OEnd implements AODigit {

	@Override
	public void increment() {
		return;
	}

	@Override
	public String stringRep() {
		return "";
	}
	
	

}
```
### Composite Usage
Client
```java
package ca.utoronto.utm.designpatterns.composite;

public class Car {
	private AODigit odometer = new OEnd();

	public Car() {
		odometer = new ODigit(odometer);
		odometer = new ODigit(odometer);
		odometer = new ODigit(odometer);
	}
	
	public void go() {
		for(int i=0;i<1005;i++) {
			System.out.println(odometer.stringRep());
			odometer.increment();
		}
	}
	public String toString() {
		return odometer.stringRep();
	}
	public static void main(String[] args) {
		Car c = new Car();
		c.go();
	}
}
```
