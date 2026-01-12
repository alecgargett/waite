I want to create a new programming language called "glua" that compiles to many languages.

To start with I would like to make the compiler than compiles to Gleam, but I would like to bear in mind that we might eventually be building compilers to target C, C++, Kotlin, F#, Jai, Zig, Go and Odin too.

I want to base glua on a design I had for a language called NatGo, that compiled to Go.

Here is a summary of **NatGo** (Version 1).

### 1. File Structure

**Standard (Implicit Boilerplate):**
```natgo
// Automatically includes 'package main' and 'import fmt'
define main():
  print "Hello";
end main() definition
```

**Bare (Manual Control):**
```natgo
bare; // Disables all auto-boilerplate (no package main, no implicit imports)
package mylib;

import "fmt";

public: // Exported function (capitalized in Go)
define add(a int, b int) returning int:
  return a + b;
end add definition
```

---

### 2. Variables & Pointers

**Variables:**
```natgo
// Immutable (Constant/Final)
declare pi = 3.14;

// Mutable Variable
declare mutable count = 0;
count = 1;

// Lists
declare list = [1, 2, 3];

// Maps
declare scores = {"Alice": 10, "Bob": 20};
```

**Pointers:**
```natgo
declare x = 10;
declare p = ref x;   // Compiles to &x (Address of)
print deref p;       // Compiles to *p (Value at)
```

---

### 3. Smart Indexing

**Symmetrical 1-Based (Math Style):**
```natgo
declare list = ["a", "b", "c"];
print list[1];   // "a" (First item)
print list[-1];  // "c" (Last item)
```

**Keyword Style:**
```natgo
print list[start];      // First item
print list[length];     // Last item
print list[start + 1];  // Second item
```

**Exclusion:**
```natgo
// Returns a new list excluding the 2nd item
declare subset = list[except 2]; 
```

---

### 4. Logic & Flow Control

**Conditionals:**
```natgo
if x > 10:
  print "Big";
else:
  print "Small";
end if

// Inverted If (Syntactic Sugar for 'if not')
unless is_ready:
  print "Waiting...";
end unless
```

**Loops:**
```natgo
// Inclusive (Run for 1, 2, 3)
from i = 1 to 3:
  print i;
end loop

// Exclusive (Run for 1, 2)
from j = 1 to before 3:
  print j;
end loop
```

---

### 5. Functions

**Definition & Calling:**
```natgo
// Named parameters (labels) are supported
define area(width float, height float) returning float:
  return width * height;
end area definition

// Calling with labels
declare a = area(width: 10.0, height: 5.0);
```

**Pipelines (Gleam Style):**
```natgo
declare name = " alice ";

using name:
  then trim;
  then uppercase;
  then print;
end using
```

---

### 6. Custom Types

**Structs:**
```natgo
define User struct:
  name string;
  age  int;
end User definition

// Instantiation
declare u = User(name: "Bob", age: 30);
```

**Interfaces:**
```natgo
define Printer interface:
  render() returning string;
end Printer definition
```

**Enums (Algebraic Data Types):**
```natgo
define Shape enum:
  is Circle(radius float);
  is Rectangle(w float, h float);
end Shape definition
```

---

### 7. Option & Result (Safety)

**Defining:**
```natgo
define find(id int) returning Option<string>:
  if id == 0:
    return None;
  end if
  return Some("Found");
end find definition
```

**Pattern Matching:**
```natgo
match result:
  when Ok(val):
    print "Success: {val}";
  when Error(msg):
    print "Failed: {msg}";
end match
```

**Guard Clauses (Early Exit):**
```natgo
// If opt is None, run the block (and return).
// If opt is Some, unwrap into 'val' and continue.
guard match opt as val else:
  print "Missing value";
  return;
end guard
```

**The "Otherwise" Operator:**
```natgo
// Use value if present, else use default
declare x = opt otherwise "default";
```

---

### 8. Go Interop & Cleanup

**Attempt (Go Error -> Result):**
```natgo
// Converts Go's native (val, err) return into Result<val, string>
declare file_res = attempt os.Open("test.txt");
```

**Ensure (Defer):**
```natgo
match file_res:
  when Ok(f):
    ensure f.Close(); // Compiles to 'defer f.Close()'
    // use f...
  when Error(e):
    print e;
end match
```

---

### 9. Concurrency

**Spawning:**
```natgo
spawn:
  print "Background task"; // Compiles to 'go func() { ... }()'
end spawn
```

**Channels:**
```natgo
declare pipe = channel<string>;

spawn:
  send "Hello" to pipe; // Compiles to 'pipe <- "Hello"'
end spawn

declare msg = receive from pipe; // Compiles to '<-pipe'
```

---

### 10. String Interpolation

```natgo
declare name = "Alice";
print "Hello, {name}"; // Compiles to fmt.Printf("Hello, %v\n", name)
```