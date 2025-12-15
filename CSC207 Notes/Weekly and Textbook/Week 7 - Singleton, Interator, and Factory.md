# Singleton
![[Pasted image 20251215002241.png]]
### Reasons
You want there to be at most, 1 instance of this class.
Offers a central, single point of access to the instance
To centralize control of the object to a single instance
### Example
Lets say we're working with a Factory design pattern class. A factory which produces a (Account, Customer, Site, Address objects) within a multithreaded environment. If the factory is instantiated in 2 separate threads, then its possible to have an object (like elderly customers), which use information from 2 seperate Factories. So it makes sense to implement the Factory as a Singleton, where only one Factory produces objects. This is common practice. 
### Singleton Class
```java
class Singleton {
	private static Singleton instance;

	private Singleton() { 
		this.count=0;
	}

	private int count;
	
	public static synchronized Singleton getInstance() {
		if (instance == null)
			instance = new Singleton();

		return instance;
	}
	public void doSomething() {	
		System.out.println(this.count++);
	}
}
```
### Singleton Usage
```java
public class UseSingleton {
	public static void main(String [] args) {
		// Singleton s1 = new Singleton(); // Singleton() is private
		Singleton s1 = Singleton.getInstance();
		Singleton s2 = Singleton.getInstance();
		
		s1.doSomething();
		s1.doSomething();
		s2.doSomething();
		s1.doSomething();
	}
}
```

# Iterator
![[Pasted image 20251215002319.png]]
### Reasons
Abstracts and captures the idea of cycling through a collection (iteration)
Since the idea is general, its supported by most programming languages. 
Used by [], ArrayList, Set, etc. so you can cycle through elements within these.

The iterator design pattern allows us to access contents of a group without exposing its structure. 
Provides multiple methods of traversal of the group
Provides an interface to interact with the group.
### Example
Imagine you have a secret collection of books and your friend wants to borrow a random book. It would be too embarrassing for them to see all the books, so you only want to reveal one of your dark secrets. So you ask them, and they ask for your 10th book. You iterate through our collection and find the 10th book. 
### Iterator Class
Native to Java, comes with the following methods.
`next()` - returns next element
`remove()` - Removes last element
`hasNext()` - Returns true if there is more elements
`forEachRemaining(Cosumer<? super E> action)` - Performs the given action for eac element until all elements have been processed. 
### Iterator Usage
```java
public class UseIterator {
	public static void main(String[] args) {

		// You have seen iterators before...
		ArrayList<Integer> ints = new ArrayList<Integer>();

		ints.add(3);
		ints.add(4);
		ints.add(7);

		// Could go through ints like this...

		for(int i=0;i<ints.size();i++){
			int k = ints.get(i);
			System.out.println(k);
		}
		
		// Alternatively
		for(int k: ints) {
			System.out.println(k);
		}
		// Get an Iterator<Integer>
		Iterator<Integer> intsIterator = ints.iterator();

		// Use the Iterator<Integer>
		while(intsIterator.hasNext()) {
			System.out.println(intsIterator.next());
		}
		
		// For created method ChangeIterator()
		ChangeIterator ci = new ChangeIterator(623);
		while(ci.hasNext()) {
			int c = ci.next();
			System.out.println(c);	
		}
		
		Change change = new Change(623);
		for(int c:change) {
			System.out.println(c);
		}
	}
}
```

# Factory
Probably the most used design pattern in modern programming. 
![[Pasted image 20251215005852.png]]
### Reasons
Want a dynamic user control of which instance of a subclass is created for example at runtime
(which is essentially, a factory produces an object for every action that is in charge of the task)
When the construction of an object is complex or involves the construction of other objects
### Example
Fast food! Imagine you are a Mcdonalds and  have a bunch of menu items. You have a factory which is your Mcdonalds, and then each McDouble, Big Mac, etc. is produced by your factory and given to the customer. The client, who is your customer, as for a Big Mac, you create the Big Mac product and give it to the client. 
### Factory Class
```java
public class Factory {
	public Food createProduct(String product){
		if(product=="Burger")return new Burger();
		if(product=="Coke")return new Soda(product);
		if(product=="RootBeer")return new Soda(product);
		if(product=="Fries")return new Fries();
		if(product=="Pizza")return new Pizza();
		if(product=="PadThai")return new PadThai();
		return null;
	}
}
```
#### Factory Subclass
Note: Food() is an abstract class which products inherit from.
```java
public class Soda extends Food {
	public Soda(String type){
		super(type, 780, 55);
		// (name, calories, carbs)
		// super comes with getters and eat() method
	}
	@Override
	public String eat(){
		return "sip";
	}
}
```
### Factory Usage
```java
public class Client {
	private Factory factory=new Factory();
	private ArrayList<Food> food = new ArrayList<Food>();
	
	
	public static void main(String [] args){
		Client client=new Client();
	}
	public Client(){
		String [] foodList={"Burger", "Fries", "Fries", "Coke", "Coke", "RootBeer", "Pizza", "PadThai", "PadThai"};
		
		for(String f:foodList){
			food.add(factory.createProduct(f));
		}
		for(Food f:food){
			System.out.println(f.getName()+" "+f.eat());
		}
	}
}
```
 