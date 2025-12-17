# Regular Expressions (Regex)
A *regular expression* is a pattern that a string may or may not match. 
Ex. [0-9]+ — Which means one or more occurrences of a number.

Strings like +, [], , , have special meanings, so when we want to use them we have to escape using a backslash. 

## Capturing Group and Backreferences
*Capturing groups* allow you to treat multiple character as as single unit. 

*Capturing Groups* are **numbered** by counting their opening parentheses from left to right. 
((A)(B(C))) have the following groups
((A)(B(C)))
(A)
(B(C))
(C)

The section of the input string matching the *capturing groups* is saved in memory for later recall via *backreference*. 
A *backreference* is specified in the regex as a backslash, followed by a digit indicating the number of the group to recall.
For example:
(\d\d)\1 = 1212 - Means the the first group of 2 digits must match the second (occur twice)
(\w*)\s\1  = asdf asdf - Means any word of 0 or more length must occur twice, separated by white space. 

When it comes to capturing groups, each group can be accessed within code. Note that group 0 is always the entire matched regex. 
### Ex. Money
```java
public class RegexExample { 
	public static void main(String[] args) { 
		String input = "the price is $345.67"; 
		// The parentheses define Group 1 
		String regex = "\\$([0-9]+(?:\\.[0-9]{2})?)"; 
		Pattern pattern = Pattern.compile(regex); 
		Matcher matcher = pattern.matcher(input); 
		if (matcher.find()) { 
			// matcher.group(0) is the full match: "$345.67" 
			// matcher.group(1) is the first captured group: "345.67" 
			String extractedNumber = matcher.group(1); 
			System.out.println("Full match: " + matcher.group(0)); 
			System.out.println("Extracted number (Group 1): " + extractedNumber); 
		} 
	} 
}
```
# JavaIO
## Streams
### Byte Streams
Raw data — no particular type of data
Audio, video, binary files, images (PNG, JPEG), etc.
Abstract Classes: `InputStream`, `OutputStream`
Ex. `System.In` (for console input), `FileInputStream`, `FileoutStream`
### Character Streams
For reading text data one character at a time. 
Each character I/O accesses the file/stream — lots of overhead.
There are character streams for the Console and separate ones for the Files.
Abstract Classes: `Reader`, `Writer`
Ex. `InputStreamReader` (InputStream in), `FileReader`, ... out equivalents.
### Buffered Streams
Optimized character streams — stores data in a buffer and only access the stream once
Ex. Used to read/write entire lines to files. 
Character Streams: `BufferedReader` (Reader in), `BufferedWriter`
Byte Streams: `BufferedInputStream`, `BufferedOutputStream`
### Scanners
For dealing with formatting input text from file or console
## Console I/O
Reading every character (very inefficent)
```java
public static void consoleIO() {
	// System class System.in, System.out
	// https://docs.oracle.com/en/java/javase/22/docs/api/java.base/java/lang/System.html
	char[] c = new char[10];

	try {
		for (int i = 0; i < c.length; i++) {
			c[i] = (char) System.in.read(); // System.in is an InputStream
		}
	} catch (IOException e) {
		System.out.println(e);
	}
	
	for (int i = 0; i < c.length; i++) {
		System.out.println(c[i]);
	}
	System.out.println();
}
```

Reading every line
```java
public static void consoleIO() {
	// Would prefer to read a line at a time...
	BufferedReader lineInput = new BufferedReader(new                                                                              InputStreamReader(System.in));
	String line;
	try {
		while ((line = lineInput.readLine()) != null) {
			System.out.println(line);
		}
	} catch (IOException e) {
		System.out.println(e);
	}
}
```

Scan the entire document and review it (slower but simpler than option 2)
```java
public static void consoleIO() {
	// Alternatively, can use a scanner
	Scanner sc = new Scanner(System.in);
	String s = sc.nextLine();
	System.out.println(s);
}
```
The `BufferedReader lineInput` line is very important. `InputStreamReader(System.in)` converts  bytes into characters. and the instantiation to BufferedReader makes it buffer, allowing for whole lines to be read. 
Keyboard → bytes → characters → lines

## File I/O
### Reading
InputStream — Abstract class is the superclass of all classes representing an input stream of bytes.
Reader — Abstract class for reading character streams
BufferedReader — Reading line oriented data 

read words and count the number of lines in the file

FileReader is used to read text (not binary data), so some encoding is done to convert from the binary data in the file to characters in Strings in Java.
```java
try {
	FileReader fr = new FileReader("words"); 

	// Buffered to make reading more efficient
	BufferedReader lineInput = new BufferedReader(fr);
	String line;
	int count=0;
	// while ((line = lineInput.readLine()) != null) {
	while(true) {
		line = lineInput.readLine();
		if(line == null)break;
				
		// System.out.println(line);
		count++;
	}
	System.out.println(count);
	fr.close();
} catch (FileNotFoundException e) {
	System.out.println(e);
} catch (IOException e1){
	System.out.println(e1);
} 
// read words and print them all out
```
### Writing
```java
public static void fileWriting() {
	// https://www.journaldev.com/878/java-write-to-file
	try {
			
		// Could use a PrintWriter as well...
		FileWriter fw = new FileWriter("stuff");
		BufferedWriter bw = new BufferedWriter(fw);
		bw.write("hi There\n");
		bw.write("Something\n");
		bw.close();
			
		// Could use a PrintWriter as well...
		PrintWriter pw = new PrintWriter("somethingAwesome");

		pw.print("That is a string");
		pw.println("This is a string");
		pw.println("This is a string");
		pw.println("This is a string");

		pw.close();
			
		pw = new PrintWriter(new FileWriter("somethingAwesome", true)); // for append
		pw.println("Some More!!");
		pw.println("Some More!!");
		pw.close();


			
	} catch (IOException e) {
		System.out.println(e);
	}
}
```

# FSMs
An FSM is a tuple consisting of M = (*q0, Q, delta, A, Sigma*). 
Where *Sigma* is the alphabet we are working with
*Q* = {q0, ... qn} is a collection of states
*delta* is a transition function which turns Q x Sigma -> Q. 
delta(q,s)=q' means if in state q, scanning symbol s then move to state q', consuming symbol s

q0 is a special start state whereas A<=Q is a collection of accepting states. 

For example, start with FSM M and input x. If M consumes every symbol of x, and ends up accepting it. Then we say M accepts x. 
If M ever gets stuck on a symbol, or we consume every symbol of x but its not enough, then M rejects x. We can represent this function *delta* by a Finite State Diagram. 

## FSM vs Regex
We will use both an FSM and Regex to determine whether the string s represents a multiple of 5.

### Regex
```java
public static boolean recognize5Regex(String s) {
	Pattern p = Pattern.compile("^\\d*(0|5)$");
	Matcher m = p.matcher(s);
	return m.matches();
}
```
Which is given string s, does s start with any number of digits and end with a 0 or a 5.
### FSM
```java
public static boolean recognize5FSM(String s) {
	char [] c=s.toCharArray();
	int len=s.length();
	// We can now access the characters of s one at a time via c[0], c[1], ..., c[len-1]
	        
	boolean isAccepted=true;
	        
	int n=0;
	int state=0; // Start out in the initial state
	while(n<len){
	    switch(state){    
	        case 0:
	            if(c[n]=='0'|| c[n]=='5')state=1;
	            else if('1'<=c[n] && c[n]<='9')state=0;
	            else state=2;
	            break;
	        case 1:
	            if(c[n]=='0'|| c[n]=='5')state=1;
	            else if('0'<=c[n] && c[n]<='9')state=0;
	            else state=2;
	            break;
	        case 2:
	            break;
	    }
	    n=n+1;
	}
	if(state!=1)isAccepted=false;
	return isAccepted;
}
```
State 2 is the fail case. Once you reach state 2, you don't escape it. You can reach state 2 by having an invalid input. But if you're in state 0 by the time the FSM checks everything, you fail as you didn't end with a 0 or 5. 

## FSM Example
Lets create an FSM for the following text file titled all.txt. We will parse the grades. 
```txt
::::::::::::::
utorid1/JugPuzzleGame/src/JugPuzzleGUIController.java
::::::::::::::
MARKS For Assignment 1, Part 2
GUI: 5/5
CODE: 5/5
END MARKS
::::::::::::::
utorid2/JugPuzzleGame/src/JugPuzzleGUIController.java
::::::::::::::
MARKS For Assignment 1, Part 2
GUI: 2.5/5
CODE: 4/5
END MARKS
::::::::::::::
utorid3/JugPuzzleGame/src/JugPuzzleGUIController.java
::::::::::::::
MARKS For Assignment 1, Part 2
GUI: 2.5/5
CODE: 4/5
END MARKS
```

```java
public class MarksParser {
	private String basePath;
	private TreeMap<String, Student> utoridToStudent=new TreeMap<String, Student>();
	private int lineNumber=0; // The line number of the file we are currently parsing
	
	public static void main(String [] args) {
		// Going to create a new file with the marks in the src/ directory
		MarksParser mp = new MarksParser("src/")
	}
	
	public MarksParser(String basePath) {
		try {
			this.basePath = basePath;
			this.parseUtorids();
			this.parseMarks();
			this.parseMarks2();
		}
		catch (IOException e){
			System.out.println(e);
		}
	}
	
	public void parseMarks() throws IOException {
		// See java.util.regex.Pattern, java.util.regex.Matcher
		
		BufferedReader inputStream = null;
		try {
			Pattern p=Pattern.compile("^(.*)/JugPuzzleGame/src/JugPuzzleGUIController\\.java$");
			
			inputStream = new BufferedReader(new FileReader(basePath+"all.txt"));

			String l;
			while ((l = inputStream.readLine()) != null) {
				Matcher m=p.matcher(l);
				if(m.matches()){
					System.out.println(l);
					System.out.println("Groupcount: " +m.groupCount());
					System.out.println(m.group(1)+" ");
				}
			}
		} finally {
			if (inputStream != null) {
				inputStream.close();
			}
		}
	}
	
	public void parseMarks2() throws IOException {
		// See java.util.regex.Pattern, java.util.regex.Matcher
		
		BufferedReader inputStream = null;
		try {
			// Regex patterns for each line type
			Pattern pColons=Pattern.compile("^:{14}$");
			Pattern pStartMarksLine=Pattern.compile("MARKS For Assignment 1, Part 2");
			Pattern pGUIMarks=Pattern.compile("^GUI:\\s*(\\d(.\\d)?)/5\\s*$");
			Pattern pCodeMarks=Pattern.compile("^CODE:\\s*(\\d(.\\d)?)/5\\s*$");
			Pattern pEndMarksLine=Pattern.compile("^END MARKS$");
			
			// utorid1/JugPuzzleGame/src/JugPuzzleGUIController.java
			Pattern pUtorid=Pattern.compile("^(.*)/JugPuzzleGame/src/JugPuzzleGUIController\\.java$");
			
			inputStream = new BufferedReader(new FileReader(basePath+"all.txt"));

			int state=0;
			// State 0 is before ":::::::"
			
			Matcher m;
			String l, utorid="";
			float guiMark=0, codeMark=0;
			lineNumber=0;
			while ((l = inputStream.readLine()) != null) {
				lineNumber++;
				switch(state){
					case 0:
						m=pColons.matcher(l);
						if(m.matches()){
							utorid=""; guiMark=0; codeMark=0;
							state=1;
						}
						break;
					case 1:
						m=pUtorid.matcher(l);
						if(m.matches()){
							utorid=m.group(1);
							state=2;
						} else {
							error("Expecting utorid line");
							return;
						}
						break;
					case 2:
						m=pColons.matcher(l);
						if(m.matches())state=3;
						else {
							error("Expecting colons");
							return;
						}
						break;
					case 3:
						m=pStartMarksLine.matcher(l);
						if(m.matches()){
							state=4;
							break;
						}
						m=pColons.matcher(l);
						if(m.matches()){
							error("Expecting start marks line");
							return;
						}
						break;
					case 4:
						m=pGUIMarks.matcher(l);
						if(m.matches()){
							guiMark=Float.parseFloat(m.group(1));
							state=5;
						} else {
							error("Expecting GUI mark");
							return;
						}
						break;
					case 5:
						m=pCodeMarks.matcher(l);
						if(m.matches()){
							codeMark=Float.parseFloat(m.group(1));
							state=6;
						} else {
							error("Expecting code marks");
							return;
						}
						break;
					case 6:
						m=pEndMarksLine.matcher(l);
						if(m.matches()){
							this.utoridToStudent.get(utorid).setGuiMark(guiMark);
							this.utoridToStudent.get(utorid).setCodeMark(codeMark);
							
							state=0;
						} 
				}
			}
			if(state!=0){
				error("Expected end of file");
			} else {
				for(String s:this.utoridToStudent.keySet()){
					System.out.println(utoridToStudent.get(s));
				}
			}
		} finally {
			if (inputStream != null) {
				inputStream.close();
			}
		}
	}
}
```
