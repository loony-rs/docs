Sure! Here are the answers to your Rust-related questions:

---

### 1. **What are the key features of Rust?**

Rust is known for its safety, performance, and concurrency. Key features include:

- **Memory safety** without a garbage collector (via ownership and borrowing).
- **Zero-cost abstractions**, meaning high-level features don’t add runtime overhead.
- **Concurrency without data races**, thanks to strict ownership rules.
- **Pattern matching**, a powerful feature for control flow.
- **Cargo & Crates.io**, a robust package management and build system.
- **Type inference**, which makes code concise while keeping it type-safe.
- **Fearless refactoring**, as the compiler prevents many common bugs.

---

### 2. **How does Rust ensure memory safety without a garbage collector?**

Rust ensures memory safety through **ownership, borrowing, and lifetimes**. Instead of using a garbage collector, Rust enforces:

- **Ownership rules** (each value has a single owner, and it gets dropped when out of scope).
- **Borrowing & References** (mutable or immutable, but not both at the same time).
- **Lifetimes** (ensuring references are always valid).  
  This prevents issues like **null pointer dereferencing, use-after-free, and double frees**.

---

### 3. **What is ownership in Rust, and how does it work?**

Ownership is Rust’s way of managing memory at compile time.

- Each value in Rust has **a single owner**.
- When the owner goes out of scope, Rust **automatically deallocates** the memory.
- Values can be **moved** (ownership transferred) or **cloned** (deep copy).  
  Example:

```rust
fn main() {
    let s1 = String::from("Hello");  // s1 owns the String
    let s2 = s1;  // Ownership moves to s2, s1 is no longer valid
    // println!("{}", s1); // ❌ This will cause a compile-time error
}
```

To keep `s1` usable, we can **clone** it:

```rust
let s2 = s1.clone();  // Deep copy, so both variables are valid
```

---

### 4. **What are borrowing and lifetimes in Rust?**

Borrowing lets you use a value without transferring ownership.

- **Immutable borrow (`&T`)** → Multiple borrows allowed.
- **Mutable borrow (`&mut T`)** → Only one borrow allowed at a time.

Example:

```rust
fn print_length(s: &String) { // Borrowing
    println!("{}", s.len());
}
fn main() {
    let s = String::from("Rust");
    print_length(&s);  // Borrow s, ownership not moved
}
```

Lifetimes ensure references **don’t outlive** their valid scope. Example:

```rust
fn longest<'a>(s1: &'a str, s2: &'a str) -> &'a str {
    if s1.len() > s2.len() { s1 } else { s2 }
}
```

Here, `'a` ensures that the returned reference is valid as long as `s1` and `s2` exist.

---

### 5. **What is the difference between `String` and `&str` in Rust?**

- **`String`** → A heap-allocated, growable string.
- **`&str`** → A reference to a string slice, usually pointing to a `String` or string literal.

Example:

```rust
let s1: String = String::from("Hello"); // Owned, heap-allocated
let s2: &str = "Hello"; // String literal, stored in binary
let s3: &str = &s1; // Borrowing a String
```

Use `String` when you need **ownership or modification**, and `&str` for **read-only views**.

---

### 6. **Explain pattern matching in Rust.**

Pattern matching is done using `match`, `if let`, and `while let`.  
Example:

```rust
enum Coin { Penny, Nickel, Dime, Quarter }
fn value(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
    }
}
```

With `if let`:

```rust
if let Some(x) = Some(5) {
    println!("Matched: {}", x);
}
```

Pattern matching is powerful for handling enums, structs, and complex data.

---

### 7. **What is `Option<T>` and `Result<T, E>`? How do they help with error handling?**

- **`Option<T>`** → Represents an optional value (`Some(T)` or `None`). Prevents null pointer dereferencing.
- **`Result<T, E>`** → Used for error handling (`Ok(T)` for success, `Err(E)` for failure).

Example:

```rust
fn divide(x: f64, y: f64) -> Option<f64> {
    if y == 0.0 { None } else { Some(x / y) }
}
```

Handling `Result<T, E>`:

```rust
fn read_file() -> Result<String, std::io::Error> {
    std::fs::read_to_string("file.txt")
}
```

These types enforce **explicit error handling**.

---

### 8. **How do you declare and use constants in Rust?**

Use `const` for **immutable compile-time constants** and `static` for **global variables**.

```rust
const PI: f64 = 3.1415;
static HELLO: &str = "Hello, Rust!";
```

Constants must have a type and cannot be mutated.

---

### 9. **What is the difference between `mut` and `const`?**

- `mut` → Allows a variable to be mutable at runtime.
- `const` → Immutable and evaluated at compile time.

Example:

```rust
let mut x = 10;  // Can change
x = 20;

const MAX: i32 = 100;  // Compile-time constant
```

---

### 10. **Explain shadowing in Rust.**

Shadowing allows reusing a variable name by redeclaring it with `let`.  
Example:

```rust
fn main() {
    let x = 5;
    let x = x + 1;  // Shadowing previous x
    let x = x * 2;
    println!("{}", x); // 12
}
```

Shadowing creates a **new variable**, whereas `mut` allows modifying the existing variable.

---

That covers all your questions! Let me know if you need further explanations. 🚀
