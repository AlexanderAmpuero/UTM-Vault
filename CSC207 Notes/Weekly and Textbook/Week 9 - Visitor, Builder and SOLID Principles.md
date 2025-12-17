# Visitor
Represents an operation to be performed on the elements. 
![[Pasted image 20251216200854.png]]
*Visitor* - The interface or abstract class used to declare the visit operations for all visitable classes. Usually the name of the operation is the same and the operations are differentiated by the method signature. 
*ConcreteVisitor* - For each type of visitor all the visit methods declared in abstract visitor must be implemented. Each visitor has different operations. 
*Visitable* - An abstraction which declares the accept operation. This is the entry point that enables an object to be visitable. 
*ConcreteVisitable* — Implements the *Visitable* interface or class and defines the accept operation. 
*ObjectStructure* — The class containing all objects that can be visited. 
### Reason
Represents an operation to be performed on the elements of an object structure.
Visitor lets you define a new operation without changing the classes of the elements on which it operates. 
### Examples
We want to create a reporting module to make a statistic about a group of customers. Each customer can have one or more orders and each order can have one or more items. GeneralReport is a visitor class and implements the IVisitor interface. 
![[Pasted image 20251216201834.png]]
### Visitor Class
```java
package ca.utoronto.utm.designpatterns.visitor;

public interface Visitor {
	public void visit(Bedroom r);
	public void visit(Washroom r);
	public void visit(Kitchen r);
}
```

```java
package ca.utoronto.utm.designpatterns.visitor;

public class CleaningVisitor implements Visitor {

	@Override
	public void visit(Bedroom r) {
		System.out.println("make bed, vacuume carpet, dust");
	}

	@Override
	public void visit(Washroom r) {
		System.out.println("clean mirror, tub/shower, clean toilet");
	}

	@Override
	public void visit(Kitchen r) {
		System.out.println("was dishes, mop floor, clean stove");
	}
}
```
Abstract
```java
package ca.utoronto.utm.designpatterns.visitor;

public abstract class Room {
	int length, width;
	Room(int length, int width){
		this.length=length;
		this.width=width;
	}
	public abstract void accept(Visitor v);
}
```
Child Room
```java
package ca.utoronto.utm.designpatterns.visitor;

public class Bedroom extends Room {
	Bedroom(){
		super(15,15);
	}
	public void accept(Visitor v) {
		v.visit(this);
        }
}
```
### Visitor Usage
```java
package ca.utoronto.utm.designpatterns.visitor;

import java.util.ArrayList;

public class House {
	private ArrayList<Room> rooms=new ArrayList<Room>();
	public void accept(Visitor v) {
		for(Room r: rooms) {
			r.accept(v);
		}
	}
	
	House(){
		rooms.add(new Kitchen());
		rooms.add(new Bedroom());
		rooms.add(new Washroom());
		rooms.add(new Bedroom());
		rooms.add(new Bedroom());
		rooms.add(new Washroom());
	}
	public static void main(String [] args) {
		House house = new House();
		CleaningVisitor cv=new CleaningVisitor();
		house.accept(cv);
	}
	
}
```
# Builder
Defines an instance for creating an object but letting subclasses decide which class to instantiate. 
![[Pasted image 20251216203314.png]]
The *Builder* specifies an abstract interface for creating parts of a *Product* object
The *ConcreteBuilder* constructs and puts together parts of the product by implementing the *Builder* interface. It defines and keeps track of the representation it creates.
The *Director* class constructs the complex object using the *Builder* interface. 
The *Product* represents the complex object thats build for the *Director*. 
### Reason
When the construction of an instance involves many different options. This would lead to a large collection of constructors. Instead, the logic for building an instance is centralized to constructor parameters. The builder can verify that the collection of parameters is consistent before building an instance. this ensures that the initial state of an instance is dynamic and consistent. 
### Examples
Students Exams. if we have an application that can be used by the students of a University which gives them a list of their grades for exams. The app would need to run in different ways depending on the user. Including log in's, button disabling and enabling, and various other items which students vary on. Each student (*Director*) would have their own unique *Product* which is based on their info. 

Note: The Builder and Abstract Factory only differ in that with the builder, it creates the object for the client, but the abstract factory gives the client the tools necessary to create their own object. 
### Builder Class
Pizza Master Builder
```java
package ca.utoronto.utm.designpatterns.builder;
// Use Fluent Interface Idiom: no 'get' and return this for chaining
public class PizzaBuilder {
	boolean extraSauce=false;
	boolean extraCheese=false;
	boolean pepperoni=false;
	boolean tomatoes=false;
	boolean pineapple=false;
	boolean bacon = false;
	boolean anchovies = false;
	
	public PizzaBuilder extraSauce(){
		this.extraSauce=true;
		return this;
	}
	public PizzaBuilder extraCheese(){
		this.extraCheese=true;
		return this;
	}
	public PizzaBuilder pepperoni(){
		this.pepperoni=true;
		return this;
	}
	public PizzaBuilder tomato(){
		this.tomatoes=true;
		return this;
	}
	public PizzaBuilder pineapple(){
		this.pineapple=true;
		return this;
	}
	public PizzaBuilder bacon(){
		this.bacon=true;
		return this;
	}
	public PizzaBuilder anchovies(){
		this.anchovies=true;
		return this;
	}
	public Pizza build(){
		// Check if the proposed Pizza is consistent, 
		// throw an IllegalStateException if not.
		
		return new Pizza(this);
	}
}
```
ConcreteBuilder1
```java
package ca.utoronto.utm.designpatterns.builder;

public class DeluxePizzaBuilder extends PizzaBuilder {
	public DeluxePizzaBuilder(){
		this.extraCheese().extraSauce().pepperoni().pineapple();
	}
}
```
ConcreteBuilder2
```java
package ca.utoronto.utm.designpatterns.builder;

public class HawaiianPizzaBuilder extends PizzaBuilder {
	public HawaiianPizzaBuilder(){
		this.pineapple().pepperoni();
	}
}
```
### Bulder Usage
Director
```java
package ca.utoronto.utm.designpatterns.builder;

import java.util.ArrayList;

public class PizzaDirector {
	private PizzaBuilder builder;
	private ArrayList<Pizza> pizzas = new ArrayList<Pizza>();
	
	public PizzaDirector(){
		PizzaBuilder plain = new PizzaBuilder();
		plain.extraCheese().extraSauce();

		pizzas.add(plain.build());
		pizzas.add(plain.build());
		pizzas.add(plain.build());
		pizzas.add(plain.build());

		PizzaBuilder hawaiian = new PizzaBuilder();
		pizzas.add(hawaiian.pineapple().bacon().build());
		pizzas.add(hawaiian.build());
	}
}
```

# SOLID
## S — Single-responsibility Principle
Every class must have a single, focused responsibility, a single reason to change.

CONSEQUENCE: Code is simpler, more classes, but each class is simple and focused

Wrong
```java
public class Employee{
  public String getDesignation(int employeeID){ // }
  public void updateSalary(int employeeID){ // }
  public void sendMail(){ // }
}
```
Right
```java
public class Employee{
  public String getDesignation(int employeeID){ // }
  public void updateSalary(int employeeID){ // }
}

public class NotificationService {
    public void sendMail() { // }
}
```
## O — Open-closed Principle
Components must be open for extension, but, closed for modification

CONSEQUENCE: Extend code via subclassing, not modifying existing classes
	: creates code that is extendable outside of code base, 
	: less likely to break existing code
Wrong
```java
public class AreaCalculator(){
  public double area(Shape shape){
    double areaOfShape;
    if(shape instanceof Square){
        // calculate the area of Square
    } else if(shape instanceof Circle){
        // calculate the area of Circle
    }
    return areaOfShape;
}
```
Right
```java
interface IAreaCalculator(){
  double area();
}

class Square implements IAreaCalculator{
  @Override
  public double area(){
    System.out.println("Calculating area for Square");
    return 0.0;
   }
}

class Circle implements IAreaCalculator{
  @Override
  public double area(){
    System.out.println("Calculating area for Circle");
    return 0.0;
   }
}
```

## L — Liskov Substitution Principle
you must be able to replace a superclass object with a subclass object without affecting the correctness of the program

CONSEQUENCE: When extending code, make sure you adhere to assumptions and
	: contracts of superclass, otherwise you may break code built for
	: superclasses assumptions

Wrong
```java
abstract class Bird{
   abstract void fly();
}

class Eagle extends Bird {
   @Override
   public void fly() { // some implementation }
}

class Ostrich extends Bird {
   @Override
   public void fly() { // dummy implementation }
}
```
Right
```java
abstract class FlyingBird{
   abstract void fly();
}

abstract class NonFlyingBird{
   abstract void doSomething();
}

class Eagle extends FlyingBird {
   @Override
   public void fly() { // some implementation }
}

class Ostrich extends NonFlyingBird {
   @Override
   public void doSomething() { // some implementation }
}
```

## I — Interface Segregation Principle
you should build small, focused interfaces that do not force the client to implement behavior they do not need

CONSEQUENCE: Code is simpler, in that dummy implementations do not need to
	: be spread throughout code base. If I is violated, to add more interfaces
	: all subclasses etc. must be reviewed and subsequently add the new interface
	: aspect

Wrong
```java
interface IShapeAreaCalculator(){
  double calculateArea();
  double calculateVolume();
}

class Square implements IShapeAreaCalculator{
  double calculateArea(){ // calculate the area }
  double calculateVolume(){ // dummy implementation }
}
```
Right
```java
interface IAreaCalculator {
    double calculateArea();
}

interface IVolumeCalculator {
    double calculateVolume();
}

class Square implements IAreaCalculator {
    @Override
    public double calculateArea() { // calculate the area }
}

class Cube implements IAreaCalculator, IVolumeCalculator {
    @Override
    public double calculateArea() { // calculate the area }

    @Override
    public double calculateVolume() {// calculate the volume }
}
```

## D — Dependency Inversion Principle
high-level modules should not depend on low-level modules

CONSEQUENCE: Violating D, leads to tight coupling between high level
	: classes and low level implementation. Leading to less flexible
	: more rigid code. Difficult to modify and extend.

Wrong
```java
public interface Notification {
    void notify();
}

public class EmailNotification implements Notification {
    public void notify() {
        System.out.println("Sending notification via email");
    }
}

public class Employee {
    private EmailNotification emailNotification; 
    public Employee(EmailNotification emailNotification) {
        this.emailNotification = emailNotification;
    }
    public void notifyUser() {
        emailNotification.notify();
    }
}
```
Right
```java
public interface Notification{
  public void notify();
}

public class Employee{
  private Notification notification;
  public Employee(Notification notification){
      this.notification = notification;
  }
  public void notifyUser(){
    notification.notify();
  }
 }

 public class EmailNotification implements Notification{
    public void notify(){
        //implement notification via email 
    }
 }

 public static void main(String [] args){
    Notification notification = new EmailNotification();
    Employee employee = new Employee(notification);
    employee.notifyUser();
 }
```