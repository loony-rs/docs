Here are the answers to your questions:

### 21. **What is unsafe Rust, and when should you use it?**

Unsafe Rust allows you to bypass some of Rust’s safety guarantees, enabling:

- Dereferencing raw pointers (`*const T`, `*mut T`).
- Calling unsafe functions (e.g., FFI or intrinsics).
- Accessing/modifying mutable statics.
- Implementing unsafe traits.
- Performing union field accesses.

Use unsafe Rust when:

- You need to interact with hardware or external systems (e.g., OS APIs, embedded development).
- You are optimizing performance by avoiding bounds checking or using manual memory management.
- You need to implement low-level abstractions (e.g., smart pointers, allocators).

### 22. **Explain the differences between `Rc<T>` and `Arc<T>`.**

Both `Rc<T>` and `Arc<T>` provide reference counting for shared ownership, but:

- `Rc<T>` (Reference Counted) is **not thread-safe**; it’s for single-threaded environments.
- `Arc<T>` (Atomic Reference Counted) is **thread-safe**, using atomic operations for reference counting.

Use `Rc<T>` when:

- The shared data is only accessed within a single thread.

Use `Arc<T>` when:

- The shared data is accessed across multiple threads.

### 23. **How do you implement a custom allocator in Rust?**

To implement a custom allocator:

1. Implement the `GlobalAlloc` trait:

   ```rust
   use std::alloc::{GlobalAlloc, Layout, System};

   struct MyAllocator;

   unsafe impl GlobalAlloc for MyAllocator {
       unsafe fn alloc(&self, layout: Layout) -> *mut u8 {
           System.alloc(layout) // Or a custom allocation strategy
       }

       unsafe fn dealloc(&self, ptr: *mut u8, layout: Layout) {
           System.dealloc(ptr, layout)
       }
   }

   #[global_allocator]
   static GLOBAL: MyAllocator = MyAllocator;
   ```

2. Mark the allocator as the global allocator using `#[global_allocator]`.
3. Optionally, use `std::alloc::Allocator` for more fine-grained memory control (introduced in Rust 1.60+).

### 24. **How does Rust's Foreign Function Interface (FFI) work?**

Rust’s FFI allows Rust to call external functions written in C or expose Rust functions to other languages.

**Calling C functions in Rust:**

1. Declare external functions using `extern "C" {}`:
   ```rust
   extern "C" {
       fn printf(format: *const u8, ...) -> i32;
   }
   ```
2. Ensure types match C ABI (e.g., use `c_int` from `std::os::raw`).

**Exposing Rust functions to C:**

1. Use `#[no_mangle]` to prevent name mangling.
2. Use `extern "C"`:
   ```rust
   #[no_mangle]
   pub extern "C" fn add(a: i32, b: i32) -> i32 {
       a + b
   }
   ```

### 25. **What is the role of procedural macros in Rust?**

Procedural macros generate Rust code at compile time. Types:

- **Function-like (`#[proc_macro]`)**: Transforms function calls, like `serde_derive`.
- **Attribute (`#[proc_macro_attribute]`)**: Transforms items (e.g., `#[derive(Debug)]`).
- **Derive (`#[proc_macro_derive]`)**: Implements traits for structs and enums.

Example procedural macro:

```rust
use proc_macro::TokenStream;

#[proc_macro]
pub fn my_macro(input: TokenStream) -> TokenStream {
    input // Modify the input tokens to generate new code
}
```

### 26. **How does the borrow checker enforce safety in multi-threaded applications?**

Rust’s borrow checker ensures **data race safety** at compile time by enforcing:

- **Ownership rules**: Data can have only one owner at a time.
- **Borrowing rules**:
  - A value can have **multiple immutable** (`&T`) references OR **one mutable** (`&mut T`) reference, but not both.
  - Ensures no data is accessed while it’s being modified in another thread.
- **Send and Sync traits**:
  - `Send`: A type can be transferred between threads.
  - `Sync`: A type can be safely accessed from multiple threads.

Example:

```rust
let data = Arc::new(Mutex::new(5));
let data_clone = Arc::clone(&data);
thread::spawn(move || {
    let mut num = data_clone.lock().unwrap();
    *num += 1;
});
```

### 27. **How does Rust's type system enable zero-cost abstractions?**

Rust’s type system enables zero-cost abstractions by:

- **Monomorphization**: Generic functions are compiled into concrete types, avoiding runtime overhead.
- **Inlining**: Compiler inlines small functions, eliminating function call overhead.
- **Move semantics**: Eliminates unnecessary copies.
- **Trait-based optimizations**: Traits allow static dispatch, avoiding virtual function overhead.

Example:

```rust
fn add<T: std::ops::Add<Output = T>>(a: T, b: T) -> T {
    a + b
}
```

At compile time, `add(2, 3)` generates optimized, type-specific machine code.

### 28. **What are higher-rank trait bounds (HRTBs) in Rust?**

HRTBs allow **generic lifetimes** in function parameters, using `for<'a>`.

Example without HRTBs (doesn't compile because lifetime is unknown):

```rust
fn apply<F>(f: F) where F: Fn(&str) {
    f("hello");
}
```

With HRTBs:

```rust
fn apply<F>(f: F) where F: for<'a> Fn(&'a str) {
    f("hello");
}
```

HRTBs are useful for **trait objects** and **closures** where lifetimes aren’t explicitly specified.

### 29. **What is the difference between `Pin<T>` and a regular reference?**

`Pin<T>` prevents types from being **moved** in memory.

- Regular references (`&T` or `&mut T`) allow moving data.
- `Pin<T>` ensures data remains in the same memory location, useful for **self-referential** types and async tasks.

Example:

```rust
use std::pin::Pin;

struct MyFuture {
    data: String,
}

impl MyFuture {
    fn poll(self: Pin<&mut Self>) {
        // Safe to use because it's pinned
    }
}
```

`Pin<T>` is useful for **async/await** and **generators** that rely on memory stability.

### 30. **How do you optimize Rust programs for performance?**

1. **Use profiling tools** (`perf`, `valgrind`, `flamegraph`) to identify bottlenecks.
2. **Minimize heap allocations** (prefer stack allocation, use `Vec::with_capacity`).
3. **Leverage `#[inline(always)]`** for small functions to avoid function call overhead.
4. **Use iterators instead of loops** for efficient, lazy evaluation.
5. **Enable LTO (Link Time Optimization)**:
   ```toml
   [profile.release]
   lto = "thin"
   ```
6. **Use `unsafe` selectively** when performance-critical.
7. **Choose appropriate data structures** (e.g., `HashMap` vs `BTreeMap`).
8. **Parallelize using Rayon**:
   ```rust
   use rayon::prelude::*;
   let sum: i32 = (1..1000).into_par_iter().sum();
   ```

Let me know if you need more details! 🚀
