# Object Oriented Design Principles
1. When a well designed OO program runs, a collection of objects are created, connect, and cooperate to get a task done. OO programming is about letting go of control, allowing objects to interact with one another without manual forcing them to do so with methods.
2. Where should a method be located? If a method mentions on concepts from some `ClassC`, then it should be within `ClassC`.
3. The default is instance variables are private, with public getters/setters within the class defined. For example:
```java
private int capacity;

   public int getCapacity(){
      return this.capacity;
   }

   public int setCapacity(int val) {
	   this.capacity = val;
   }
```
4. Choose appropriate methods to make public. They typically take an instance and change their state.
5. What is the main purpose of a class? To maintain the consistent state of its instance variables, by changing them within. 
6. Why should I not allow access to instance variables? I will answer this with an example, if I wanted to determine if you're hungry, would I look into your stomach and check myself? Or would I ask you.

# Interface Continued
As mentioned previously, Java does not allow you to inherit from multiple super classes, only one at a time. This topic is addressed by *interfaces*, which are not classes, and allow you to specify a set of **method signatures** that a class must define for the implementation.
For example, a Smart TV can be used to implement the following interfaces:
•AirPlay
•Bluetooth
•WiFi
•ScreenMirroring
•Google Assistant/Alexa
## Interface Example
Lets construct an *interface* called `Sortable`, which contains the key ideas of Sorting, comparing, and swapping. (`compare` and `swap`)
We've chosen the attributes of comparing and swapping, because all sortable things have these ideas in common. (Numbers, people, words, etc.)