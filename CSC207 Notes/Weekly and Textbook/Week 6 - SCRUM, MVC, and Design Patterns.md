In this course we discuss various project development methodologies, with 2 primary candidates.
Those are **SCRUM** and **Waterfall**
The **Waterfall** methodology follows a top down life cycle which goes as follows,
- Assess and analyze the requirements of the problem at hand
- Design the product/solution
- Built it
- Test it
- Maintain it
This is a linear progression, which mimics a waterfall, flowing from one step to another.
The **SCRUM** methodology is the workflow this course will follow. 

$$
\begin{align}
\text{testing LATEX}
\end{align}
$$
# SCRUM
No, it is not an acronym, it is a word derived from various sports (like football), where a team comes together, plans, executes a play, and pauses, coming together again to prepare for the next push. These pushes continue until the team reaches the goal, and they are called *Sprints* in the development cycle. 
For a software dev, the ball is the product that's being developed, and the target is the rollout to the market. 
## Roles
**Product Owner** - These are key stake holders, and they prioritize the final protect and communicate the vision of the product.
**SCRUM master** - This is the team lead, they organize the team and lead discussions on obstacles, assuring the owner's goals are met in the timeline.
**Development Team** - These are designers, analysts, and devs, who design, build and test the product as the **SCRUM master** demands. 

## SCRUM Workflow
1. Create a product backlog
	- This is a master list of features, tasks, improvements, and bugs.
	- It also includes *User story*'s, which are simple descriptions of a feature from the perspective of the end user
	- This is in the follow structure:
		- Story Name: GIVEN BY PRODUCT OWNER  
		  Priority: GIVEN BY PRODUCT OWNER  
		  Time Estimate: GIVEN BY TEAM  
		  
		  As a : TYPE OF USER,  
		  I want : TO PERFORM SOME TASK  
		  So that : I CAN ACHIEVE SOME GOAL/BENEFIT/VALUE
2. Sprint Planning
	- Break down the items in the product backlog into groups/sprints
	- Each sprint should have a broader goal to be accomplished once the items are complete
3. The Sprint itself
	- The team works on the planned items, with a partial working product at them end.
	- There should be "daily" *stand-ups*, where team members discuss issues and help one another stay on track.
4. Sprint Review
	- The deliverables for the sprint are presented to the stakeholders for feedback
5. Sprint Retrospective and Repeat
	- Look over everything accomplished and acknowledge what failed. Now repeat the sprint process using lessons from the retrospective, working on the next set of items in the backlog.
![[Pasted image 20251023222932.png]]
## SCRUM Strengths
- Iterative - Allows for more adaptability
- Stakeholder-focused - Allows for team to respond to stakeholders' changing needs
- Continuously delivers - Partial working product ensure alignment among developer and stakeholder understanding.
- Constant feedback - Both in terms of product and team effectiveness, reducing overall costs.

# Design Patterns
When code is poorly designed, i.e., not modular or flexible code design, it becomes difficult to collaborate and build a project since you are always waiting for someone else to finish their part.

Design patterns are a notion of structuring/arranging code to be flexible and modular:
- It describes the code structure, not the implementation details
- Language-agnostic (means it's not limited to Java)
## Intro Example
![[Pasted image 20251023223544.png]]
Take the above example: Let's say we have the left GUI already built, and we want to turn it into the right GUI. Will it be easy to do? Or will we have to overhaul the entire codebase.
The answer is simple, if the code is organized and designed well, then it should be simple. Otherwise we're in for a long, sad, coding sesh.

# Observer/Observable
## Reasons
O Wants to react to changes in state of object M without having to burden M with what O needs from M to correctly react.
Allow many such O's to independently subscribe to M for an update when M changes.
## Logic
The *observable* knows nothing about the *Observer*, it just understands that the *Observer* is watching it's `update()` method.
![[Pasted image 20251215003120.png]]
Note: `ConreteObservableA` and `ConreteObservableB` are mislabeled, and should be *Observers*
# MVC - Model, View and Controller
**Model** - The Data & Logic centre
- Handles the data and business logic (database operations, rules, and computations)
- Defines how data is structured, stored, and modified
**View** - The visual elements that display the model's information
- Manages what the user sees (UI, templates, web pages)
- Doesn't know how data is managed. Never touches the data directly
**Controller**
- Interprets the user actions, updating the model and selecting which view to display
- Connect the users actions between the model and view
- Interprets what the user wants and tells the model and view to update accordingly.
![[Pasted image 20251214222601.png]]
## Example - Balloon
Lets create a basic MVC for a balloon:
First, we create the model, that is, the balloon.
```java
public class Balloon extends Observable {
	private String colour;
	private int volume = 0;
	public boolean popped = false;
	private static final int MAX_VOLUME = 75;
	
	public Balloon(String colour, int volume) {
		this.colour = colour;
		if (volume > MAX_VOLUME) {
			this.pop();
		} else {
			this.volume = volume;
		}
	}
	
	public int getVolume() {
		return this.volume;
	}
	private boolean checkVolume(int v) {
		return ((this.volume + v) <= MAX_VOLUME) && v >= 0;
	}
	
	public void pump(int v) {
		if (checkVolume(v) && !this.popped) {
			this.volume += v;
		} else {
			this.pop();
		}
		this.setChanged();
		this.notifyObservers();
	}
	
	public void pop() {
		this.volume = 0;
		this.popped = true;
		this.setChanged();
		this.notifyObservers();
	}
}
```
Perfect! Now we have a working **Model** for our balloon. Now, we will create a **Controller**
```java
package ca.utoronto.utm.balloon;

public class Controller implements EventHandler<actionEvent> {
	private Balloon model;
	
	public Controller(Balloon model) {
		this.model = model;
	}
	
	public void Handle(actionEvent e) {
		model.pump(5);
	}
}
```

```java
package ca.utoronto.utm.balloon;

public class View implements Observer {
	public Button inflate = Button("Inflate");
	public Label balloon = Label("Pump me!");
	
	public void update(Observable o, String args) {
		this.model = (Balloon) o;
		if (this.model.isPopped) {
			balloon.setText("You popped me :C");
		} else {
			balloon.setText("Pump me!" + this.model.getVolume());
		}
	}
}
```

```java
package ca.utoronto.utm.balloon;

public class App extends Application {
	public static void main(String[], args) {
		launch(args);
	}
	private Balloon model = new Balloon();
	private Controller controller = new Controller(model);
	private View view = new View(model);
	
	public void start(Scene scene) throws Exception {
		// MODEL
		model.setObserver(view);
		// CONTROLLER
		view.inflate.setOnAction(controller);
		
		// VIEW
		HBox root = new HBox(view.balloon, view.inflate);
		
		// SCENE
		Scene scene = new Scene(root, 200, 200);
		Stage.setScene(scene);
		Scene.show();
	}
	
}
```


In the context of JavaFX, we consider `Eventhandlers` and Buttons to be GUI parts. 

As practice for Design Patterns, Benedetti has providing a badly structured clock app, and I must restructure it to be MVC-compliant. Can't wait!

#### Watch out for testing and test avg was 70 ish
