
# Semantic Analysis Design (C Implementation)

## 1. Symbol-Table Structure
Since the compiler is written in C, the symbol table will be implemented using a **Linked List of Structs** (or an Array of Structs). To handle variable scopes, we will maintain a stack of these symbol tables, where each new block scope pushes a new table onto the stack.

## 2. Information Stored for Each Variable
Each symbol table entry will be a `struct` containing the following fields:
*   `char* name;` (The identifier string)
*   `DataType type;` (An enum representing int, bool, string)
*   `int scope_level;` (Integer representing the nesting depth)
*   `int line_declared;` (Line number for error reporting)
*   `struct SymbolEntry* next;` (Pointer to the next entry in the linked list)

## 3. Variable Declaration and Lookup Procedures
*   **Declaration:** When the parser encounters a declaration, it calls `insert_symbol()`. This function checks the current scope's linked list using `strcmp()`. If the name exists, it throws an error. If not, it allocates memory for a new `struct` and adds it.
*   **Lookup:** When a variable is used, the compiler calls `lookup_symbol()`. This function searches the current scope's list, then traverses up the scope stack. If it reaches the global scope and finds nothing, it returns `NULL`, triggering an undeclared variable error.

## 4. Declaration-Before-Use Rules
The C parser enforces this by calling `lookup_symbol()` every time an identifier is parsed in an expression. If the function returns `NULL` (meaning the variable has not been declared in the current or parent scopes), the compiler halts and reports a semantic error.

## 5. Supported Data Types
We will use a C `enum` for data types. The supported types are:
*   `TYPE_INT`
*   `TYPE_BOOL`
*   `TYPE_STRING`

## 6. Type-Checking Rules
*   **Arithmetic Expressions:** The AST (Abstract Syntax Tree) node for arithmetic operations (+, -, *, /) will check if both the left and right child nodes are of type `TYPE_INT`. If not, a type mismatch error is generated.
*   **Assignments:** The AST assignment node will verify that the `type` of the left-hand variable matches the `type` of the evaluated right-hand expression.

## 7. Handling Duplicate Declarations
The `insert_symbol()` function in C will use `strcmp()` to compare the new variable name with existing names in the current scope's linked list. If `strcmp()` returns 0 (a match), a duplicate declaration error is thrown.

## 8. Semantic Error Messages and Reporting
Errors will be reported using `fprintf(stderr, ...)` in C. The format will be:
`[Semantic Error] Line %d: %s\n` (where `%d` is the line number and `%s` is the specific error message).

---

## Deliverable: 5 Semantic Error Examples

Here are 5 examples of MiniLang code that will cause semantic errors, along with the expected compiler outcomes.

### Example 1: Duplicate Declaration
```

int x = 5;
int x = 10;

```
*Expected Outcome:* `[Semantic Error] Line 2: Variable 'x' already declared in this scope.`

### Example 2: Undeclared Variable
```

y = 20;

```
*Expected Outcome:* `[Semantic Error] Line 1: Variable 'y' used before declaration.`

### Example 3: Type Mismatch in Assignment
```

int score = "high";

```
*Expected Outcome:* `[Semantic Error] Line 1: Type mismatch. Cannot assign string to variable of type int.`

### Example 4: Invalid Arithmetic Operation
```

bool isReady = true;
int total = isReady + 10;

```
*Expected Outcome:* `[Semantic Error] Line 2: Invalid arithmetic operation. Operand 'isReady' must be of type int.`

### Example 5: Using an Uninitialized Variable
```

int a;
int b = a + 5;

```
*Expected Outcome:* `[Semantic Error] Line 2: Variable 'a' may be uninitialized before use.`


