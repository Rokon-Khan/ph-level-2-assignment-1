# 1. What are some differences between interfaces and types in TypeScript?

TypeScript offers two powerful tools for defining shapes of data: **interfaces** and **types**. While they often seem interchangeable, understanding their differences can help you make informed decisions about when to use each.

### Key Differences

### **1. Declaration Syntax**

- **Interfaces**: Defined using the interface keyword. They are primarily used to describe object shapes or class contracts.
    
```
interface User {
  name: string;
  age: number;
}
```

    
- **Types**: Defined using the type keyword with a broader scope, allowing for more flexible type definitions, including unions, intersections, and primitives.
    
    ```
    type User = {
      name: string;
      age: number;
    };
    ```
    

### **2. Extending and Merging**

- **Interfaces**: Support **declaration merging**, meaning you can redefine an interface with the same name, and TypeScript will merge the declarations. This is particularly useful for extending third-party library types.
    
    ```
    interface User {
      name: string;
    }
    interface User {
      age: number;
    }
    *// Merged: User has both name and age*
    const user: User = { name: "Alice", age: 30 };
    ```
    
- **Types**: Do not support declaration merging. However, you can combine types using **intersection** (&) or **union** (|) operators.
    
    `type Name = { name: string };
    type Age = { age: number };
    type User = Name & Age; *// Intersection*
    const user: User = { name: "Bob", age: 25 };`
    

### **3. Flexibility**

- **Interfaces**: Best suited for defining object shapes and contracts for classes. They are less flexible for complex type manipulations.
    
    ```
    interface Printable {
      print(): string;
    }
    class Book implements Printable {
      print() {
        return "Printing book";
      }
    }
    ```
    
- **Types**: More versatile, supporting union types, primitive types, and complex type operations.
    
    ```
      type ID = string | number; *// Union type*
      type Status = "active" | "inactive"; *// Literal type*
    ```
    

### **4. Extensibility**

- **Interfaces**: Use extends to inherit from other interfaces.
    
    ```
    interface Person {
      name: string;
    }
    interface Employee extends Person {
      role: string;
    }
    const employee: Employee = { name: "Charlie", role: "Developer" };
    ```
    
- **Types**: Use intersection (&) to combine types, but cannot use extends.
    
    ```
    type Person = { name: string };
    type Employee = Person & { role: string };
    const employee: Employee = { name: "Dave", role: "Manager" };
    ```
    

### When to Use Each

- **Use Interfaces**:
    - When defining object shapes or class contracts.
    - When you need declaration merging (e.g., extending library types).
    - When prioritizing readability for object-oriented programming.
- **Use Types**:
    - When working with unions, intersections, or primitive types.
    - When you need flexibility for complex type logic.
    - When defining utility types or aliases for non-object types.

### Example: Combining Both

```
interface Person {
  name: string;
}
type Contact = { email: string };
interface Employee extends Person {
  details: Contact;
}
const employee: Employee = {
  name: "Eve",
  details: { email: "eve@example.com" },
};
```

### Conclusion

Interfaces and types are both essential in TypeScript, but they serve slightly different purposes. Interfaces shine in object-oriented scenarios and declaration merging, while types offer unmatched flexibility for diverse type definitions. Choose based on your project’s needs, but don’t hesitate to use both when appropriate.

# 2. Explain the difference between any, unknown, and never types in TypeScript

TypeScript’s type system includes special types—any, unknown, and never—that handle edge cases and ensure type safety. Misusing these types can lead to bugs or unsafe code, so let’s explore their differences and best practices.

### **1. The any Type**

The any type is TypeScript’s escape hatch, disabling type checking for a value. It’s useful when dealing with dynamic or untyped data but sacrifices type safety.

- **Behavior**: A variable of type any can hold any value, and you can perform any operation on it without TypeScript complaining.
- **Use Case**: Interfacing with untyped JavaScript libraries or migrating legacy code to TypeScript.
- **Risk**: Overusing any undermines TypeScript’s benefits, as it bypasses type checking.

**Example**:

```
let data: any = "Hello";
data = 42; *// No error*
data.foo(); *// No error, but runtime error if method doesn’t exist*
```

### **2. The unknown Type**

The unknown type is a safer alternative to any. It represents a value of any type but requires type checking or narrowing before performing operations.

- **Behavior**: You cannot perform operations on an unknown value without first narrowing its type (e.g., using typeof, type guards, or assertions).
- **Use Case**: Working with API responses or user input where the type is initially unknown.
- **Advantage**: Enforces type safety, reducing runtime errors.

**Example**:

```
let input: unknown = "Hello";
*// input.toUpperCase(); // Error: Object is of type 'unknown'*
if (typeof input === "string") {
  console.log(input.toUpperCase()); *// Safe: HELLO*
}
```

### **3. The never Type**

The never type represents a value that **never occurs**. It’s used for functions that never return or scenarios that are impossible.

- **Behavior**: A function with a never return type either throws an error, runs indefinitely, or is unreachable.
- **Use Case**: Exhaustive type checking in unions or functions that always throw.
- **Advantage**: Helps ensure all cases are handled in type-safe code.

**Example**:

```
function throwError(message: string): never {
  throw new Error(message);
}

function exhaustiveCheck(value: string | number): string {
  if (typeof value === "string") return "String";
  if (typeof value === "number") return "Number";
  throwError("Unreachable"); *// never*
}
```

### Comparing the Three

| Type | Description | Type Safety | Use Case Example |
| --- | --- | --- | --- |
| any | Any value, no type checking | Low | Legacy code, untyped libraries |
| unknown | Any value, requires type narrowing | High | API responses, dynamic inputs |
| never | Value that never occurs | High | Error-throwing functions, type guards |

### Best Practices

- **Avoid any**: Use unknown or specific types instead to maintain type safety.
- **Use unknown for dynamic data**: Narrow the type with checks (e.g., typeof, instanceof) before use.
- **Leverage never for exhaustiveness**: Ensure all cases in unions are handled, catching logical errors at compile time.

### Example: Practical Application

```
function processInput(input: unknown): string {
  if (typeof input === "string") {
    return input.toUpperCase();
  } else if (typeof input === "number") {
    return input.toString();
  }
  throwError("Invalid input"); *// never*
}

console.log(processInput("hello")); *// HELLO*
console.log(processInput(42)); *// "42"// processInput(true); // Throws error*
```

### Conclusion

The any, unknown, and never types serve distinct purposes in TypeScript. Use any sparingly to avoid losing type safety, embrace unknown for handling dynamic data with checks, and leverage never to enforce exhaustive logic. Mastering these types will make your TypeScript code safer and more robust.
