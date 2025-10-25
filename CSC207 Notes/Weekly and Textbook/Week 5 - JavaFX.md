# JavaFX
JavaFX is a modern toolkit that develops use to build GUI's in Java (the course used to use Swing but its old now)
## Main Components
All JavaFX GUIs have 3 main components, that is, 
- **Stage** - The main window
- **Scene** - A container for content that will be displayed
	- A Scene can only hold one *node*, but you can create many scenes/pages and connect them to the **Stage** as needed.
- **Node** - The visual elements of the scene.
	- Button
	- TextField
	- Label
	- ComboBox
	- ListView
## Layout
![[Pasted image 20251023163001.png]]Above we have a potential layout design, note that the "Layout Pane" is just a *Node* that contains every other node. We can consider it as the **Root Node**. This Layout Pane is a special node which provides the logic to rearrange and resize all of the nodes within.

- HBox: horizontal box – adds additional nodes to the right of the previous one
- Vbox: vertical box – adds additional nodes below the previous one
- BorderPane: divide layout into 5 groups (top, bottom, left, right, center)
- GridPane: creates a grid-like structure for nodes to be placed
- StackPane: for stacking elements on top of each other

Now, here is a potential Layout which expands on the above example.
![[Pasted image 20251023163407.png]]
![[Pasted image 20251023163422.png]]
### Simple GUI
This above design can be developed with the following code:
```java
VBox root = new VBox();

Label l1 = new Label(“This is the top node”);
root.getChildren().add(l1);

HBox hb = new HBox();
VBox vb = new VBox();

Button b1 = new Button(“Button 1”);
Button b2 = new Button(“Button 2”);
Button b3 = new Button(“Button 3);

vb.getChildren().addAll(b1,b2,b3);

TextField text1 = new TextField();

hb.getChildren().addAll(vb, text1);
root.getChildren().add(hb);
```
This portion is rather intuitive, as we are creating a VBox() as our root where everything is stored, and creating a "This is the top node" label at the top of the box. 
Then,  we create a HBox() which will store everything later, and a VBox() for the 3 buttons.
We then create the 3 buttons and create one TextField() to go to the right of the HBox() we declared earlier.
Note that the following are commonly used imports and the essentials to build a GUI with JavaFX. Accompanied with a minimal GUI.
```java
import javafx.application.Application;
import javafx.scene.Scene;
import javafx.stage.Stage;

// the following are unneccesary, but commonly used
import javafx.scene.layout.*;
import javafx.scene.control.Label;
import javafx.scene.control.Button;
import javafx.scene.control.TextField;

public class myFirstGUI extends Application {
	@Override
	public void start(Stage stage) throws Exception {
		VBox root = new VBox();
		Label l1 = new Label("This is the top node");
		root.getChildren().add(l1);
		
		Scene s = new Scene(root, 300, 250);
		stage.setScene(s);
		stage.show();
	}
	
	public static void main(String[] args) {launch(args);}
}
```

Or use this general structure for the `start` method:
![[Pasted image 20251023164642.png]]
## EventHandlers
An example of an **EventHandler** is the button mentioned earlier in the GUI layout. Here we created a button.
An **EventHandler** determines which interaction a user is interested in, and implements the appropriate *interface*. 
`Interface EventHandler<T extends Event>`
`supplying method`
`void handle(T event)`
It must connect the button to the node via the following code snippets:
    a) n.addEventHandler(EventType, handler);
    b) n.setOnXXX(handler) or n.onXXX(handler)
Where n is the node.
### Example
0. We want to handle the Button click, use `Button extends BaseButton extends Node`
	This node is aware of many events (onDragXXX, onKeyXXX, onMouseXXX)
	It also has the method `setOnAction()` which we want to register to  an event handler using:
	`final void setOnAction(EventHandler<ActionEvent> value)` which sets the value of the property *OnAction*.
1. We need to implement the `EventHandler<ActionEvent> value`, that is, my event handler would need to implement Interface `EventHandler<T extends Event>` supplying method `void handle(T event)`.
	Which in this case is: I need to implement
    Interface `EventHandler<ActionEvent>`
    supplying method `void handle(ActionEvent event)`
2. Once I implement this interface, I can register with the button
	`n.addEventHandler(EventType<ActionEvent>, handler);`
	`n.setOnAction(handler)`
	