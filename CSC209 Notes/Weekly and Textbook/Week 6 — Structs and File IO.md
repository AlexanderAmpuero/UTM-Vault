# PCRS
## Linked Structure and Iteration
### Linked Structures vs. Arrays
While arrays are fixed-size and contiguous in memory, **linked structures** grow and shrink dynamically. Instead of relying on physical proximity, they use **pointers** to connect separate memory blocks called **nodes**.
#### The Anatomy of a Node
In C, a node is a `struct` containing two distinct parts:
1. **Data:** The actual value you want to store (int, float, etc.).
    
2. **Next Pointer:** A pointer to another struct of the same type.
    
The list starts with a **front pointer** (or "head") which holds the address of the first node. The final node’s next pointer is set to `NULL`, signalling the end of the sequence.

|**Feature**|**Arrays**|**Linked Lists**|
|---|---|---|
|**Size**|Static (fixed at declaration)|Dynamic (changes at runtime)|
|**Memory**|Contiguous (side-by-side)|Scattered (linked via pointers)|
|**Access**|Instant via index (`arr[i]`)|Sequential (must traverse from front)|

---
### Traversing the List

Since nodes are scattered, you can't use `list[5]`. You must "walk" through the list starting from the head. This is called **traversing**.
#### The Traversal Loop
We use a temporary pointer (often called `curr`) to move from node to node without losing the starting position of our list.


```C
Node *curr = front;
while (curr != NULL) {
    printf("%d\n", curr->data);
    curr = curr->next; // Move to the next link
}
```

> **Pro Tip:** Never use your actual `front` pointer to traverse. If you move `front`, you lose the reference to the start of your list, and those nodes become "lost" in memory (a memory leak).

---
### Inserting Nodes
Insertion is a surgery of pointers. The most critical rule: **Order matters.** If you break the old link before establishing the new one, you lose the rest of your list.
#### Steps for Middle/End Insertion:
1. **Traverse:** Find the node _before_ the insertion point (`curr`).
    
2. **Create:** Allocate a new node (`new_node`).
    
3. **Link Forward:** Set `new_node->next = curr->next`. (This connects the new node to the "tail").
    
4. **Link Backward:** Set `curr->next = new_node`. (This connects the "head" part to the new node).
---
### The "Head" Problem: Pointers to Pointers
Inserting at **Position 0** is a special case because there is no node _before_ it. You must update the `front` pointer itself.

#### Why `Node **front_ptr`?

If you pass `front` to a function, C creates a **copy** of that pointer. Changing that copy inside the function won't change the actual `front` variable in `main()`. To modify the original pointer, you must pass its **address**.
#### Correct Front Insertion:

```C
void insert_front(Node **front_ptr, int val) {
    Node *new_node = create_node(val, *front_ptr); // Point new node to old head
    *front_ptr = new_node; // Update the caller's actual front pointer
}
```

This "pointer to a pointer" (double pointer) allows the function to reach back into the caller's scope and swap the starting address of the list.
## Useful C Features
C provides two powerful tools for creating aliases: **typedef**, which is handled by the compiler, and **macros**, which are processed by the pre-processor before compilation begins.

---
### Type Aliasing with `typedef`

A `typedef` creates a new name for an **existing type**; it does not create a brand-new type like a `struct` does.

- **Purpose**: It enhances readability by giving context to standard types (e.g., aliasing `unsigned int` as `shoe_size_t`).
    
- **Structs**: It is commonly used to simplify `struct` declarations, allowing you to avoid typing the `struct` keyword every time you define a variable.
    
- **Syntax**: You can combine a struct definition and alias: `typedef struct { ... } Student;`.
---
### Text Substitution with Macros

Macros use the `#define` directive to perform direct string replacement in your code.

- **Constants**: They are ideal for replacing "mysterious numbers" (magic numbers) with meaningful names like `MAX_NAME_LENGTH`.
    
- **Parameters**: Macros can act like tiny, efficient functions. However, because they are simple text replacements, they do not use `=` or `;`.
`#define WITH_TAX(x) ((x) * 1.08)`
Where you must write:
`printf("%f\n", (purchase1+purchase2 * 1.08));`

---
### The "Gotchas" of Macros
Because macros are handled before compilation, they can lead to subtle bugs.

1. **No Spaces**: In a macro with parameters, there must be **no space** between the macro name and the opening parenthesis. A space causes the pre-processor to treat the parentheses as part of the replacement text instead of a parameter list.
    
2. **Parentheses are Mandatory**: Always wrap the parameter and the entire macro expression in parentheses.
    
    - **The Risk**: If you pass an expression like `a + b` into a macro without parentheses, operator precedence (like multiplication) might happen before the addition, leading to incorrect math.
---
## The C Preprocessor
Directives starting with `#` are processed _before_ compilation via raw text substitution, modifying your source code into a stream of tokens for the compiler.
### Preprocessor Directives and Macros
The `#define` directive creates macros that replace strings with specified values or code.

- **Constants**: Macros should be capitalized by convention and wrapped in parentheses to prevent order-of-operation errors.
    
- **Predefined Macros**: Systems provide macros like `__FILE__` and `__LINE__` for metadata, or `__APPLE__` and `linux` for platform-specific logic.
    
- **File Inclusion**: `#include` literally copies the contents of a header file into the source. If headers aren't "guarded" using conditionals, including them twice leads to "multiply defined symbol" errors.
---
### Conditional Compilation

You can use `#if`, `#ifdef`, and `#ifndef` to control which code reaches the compiler.

- **System Logic**: Use conditionals to include different libraries or constants based on the OS.
    
- **Debugging**: Wrap `printf` statements in `#ifdef DEBUG`. You can then toggle these on during compilation using the `-DDEBUG` flag without changing the source code.
---
### Function-Like Macros: Power and Peril
Function-like macros take arguments and can be more efficient than functions, but they are notoriously difficult to debug.
- **Side Effects**: Arguments are not evaluated once (as in functions) but are copied literally. Passing `++x` to a macro like `GREATER(a, b)` can cause `x` to increment twice.
    
- **Parentheses**: You must wrap every parameter _and_ the entire macro in parentheses to avoid operator precedence bugs.
    
- **`do { ... } while(0)`**: This pattern is used to wrap multi-statement macros, ensuring they behave like a single statement that requires a semicolon.
    
- **Stringification (`#`)**: Placing a `#` before a macro parameter converts the argument into a string literal.

> **The Verdict**: Because macros are type-blind and ignored by most debuggers, use C language features (like `inline` functions) whenever possible.

## Function Pointers
Functions in C are not limited to being blocks of code; they can also be treated as **data**. Function pointers allow developers to pass functions as arguments, store them in structures, or return them from other functions, enabling more dynamic and generic code.

---
### Motivation: Generic Functions
Function pointers are often used to create generic "wrapper" functions that can execute different logic based on their arguments.

- **Timing Example**: Instead of hard-coding a specific sort into a timing function, you can pass a function pointer as a parameter. This allows one `time_sort` function to measure the performance of bubble sort, insertion sort, or any other algorithm with a matching signature.
    
- **Signatures**: For a function pointer to be valid, it must match the **signature** (return type and the number/type of arguments) of the target function.
---
### Syntax and Declaration
Declaring a function pointer requires specific syntax to distinguish it from a function that returns a pointer.

- **Variable Declaration**: The pointer name must be wrapped in parentheses and preceded by an asterisk: `void (*my_func_ptr)(int *, int)`.
    
- **Function Names as Pointers**: Much like an array name is treated as a pointer to its first element, a function name is treated as a pointer to that function.
    
- **Calls**: You can invoke a function via its pointer using standard function call syntax (e.g., `my_func_ptr(arr, size)`) without needing to explicitly dereference it.
---
### Advanced Applications
Function pointers enable complex architectural patterns, such as command-line selection of operations or early forms of objects.

|**Concept**|**Application**|
|---|---|
|**Function Arrays**|Storing multiple function pointers in a struct array (e.g., `SORTS[]`) to let a user choose which function to run via command-line arguments.|
|**Returning Pointers**|Functions can return pointers to other functions, though the syntax is complex as the name and arguments are placed in the middle of the return type.|
|**`typedef` Aliasing**|Using `typedef` to create an alias for a function pointer type (e.g., `SortFunc_t`) significantly cleans up code and makes declarations easier to read.|

> **Connection to Objects**: Storing data and the functions that handle that data within the same `struct` is a precursor to the concept of an "object" found in object-oriented programming.