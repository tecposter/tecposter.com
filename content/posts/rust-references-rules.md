---
date: 2024-07-14T01:12:00.180000981Z
title: Rust References Rules
---

References Rules / Borrowing Rules

1.  At any given time, we can have either one mutable reference or any
    number of immutable references.
2.  References must always be valid

# Rule 1

At any given time, we can have either one mutable reference or any
number of immutable references.

Breaks this rule will cause errors:

-   E0499: A variable was borrowed as mutable more than once.
-   E0596: This error occurs because you tried to mutably borrow a
    non-mutable variable.
-   E0502: A variable already borrowed as immutable was borrowed as
    mutable.

## E0499

Erroneous code example:

``` rust
let mut i = 0;
let mut x = &mut i;
let mut a = &mut i;
x;
// error: cannot borrow `i` as mutable more than once at a time
```

Output

``` shell
error[E0499]: cannot borrow `i` as mutable more than once at a time
 --> src/main.rs:4:17
  |
3 |     let mut x = &mut i;
  |                 ------ first mutable borrow occurs here
4 |     let mut a = &mut i;
  |                 ^^^^^^ second mutable borrow occurs here
5 |     x;
  |     - first borrow later used here

For more information about this error, try `rustc --explain E0499`.
```

## E0596

This error occurs because you tried to mutably borrow a non-mutable
variable.

Erroneous code example:

``` rust
let x = 1;
let y = &mut x; // error: cannot borrow mutably
```

To fix

``` rust
let mut x = 1;
let y = &mut x;
```

## E0502

A variable already borrowed as immutable was borrowed as mutable.

Errorneous code example

``` rust
let mut s = String::from("hello");

let r1 = &s; // no problem
let r2 = &s; // no problem
let r3 = &mut s; // BIG PROBLEM

println!("{}, {}, and {}", r1, r2, r3);
```

Error output

``` shell
error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
 --> src/main.rs:6:14
  |
4 |     let r1 = &s; // no problem
  |              -- immutable borrow occurs here
5 |     let r2 = &s; // no problem
6 |     let r3 = &mut s; // BIG PROBLEM
  |              ^^^^^^ mutable borrow occurs here
7 | 
8 |     println!("{}, {}, and {}", r1, r2, r3);
  |                                -- immutable borrow later used here

For more information about this error, try `rustc --explain E0502`.
error: could not compile `ownership` due to previous error
```

# Rule 2

References must always be valid.

[Dangling
References](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html#dangling-references):
In languages with pointers, it's easy to erroneously create a dangling
pointer, a pointer that references a location in memory that may have
been given to someone else, by freeing some memory while preserving a
pointer to that memory. In Rust, by contrast, the compiler guarantees
that references will never be dangling references: if you have a
reference to some data, the compiler will ensure that the data will not
go out of scope before the reference to the data does.

Breaks the rule 2 will cause errors

-   E0106: This error indicates that a lifetime is missing from a type.
-   E0507: A borrowed value was moved out.

## E0106

This error indicates that a lifetime is missing from a type. If it is an
error inside a function signature, the problem may be with failing to
adhere to the lifetime elision rules.

Errorneous code examples

``` rust
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");
    &s
}
```

## E0507

-   A borrowed value was moved out.
-   Moving a member out of a mutably borrowed struct

Erroneous code examples:

``` rust
use std::cell::RefCell;

struct TheDarkKnight;

impl TheDarkKnight {
    fn nothing_is_true(self) {}
}

fn main() {
    let x = RefCell::new(TheDarkKnight);
    x.borrow().nothing_is_true(); // error: cannot move out of borrowed content
}
```

``` shell
error[E0507]: cannot move out of dereference of `Ref<'_, TheDarkKnight>`
  --> src/main.rs:11:5
   |
11 |     x.borrow().nothing_is_true(); // error: cannot move out of borrowed content
   |     ^^^^^^^^^^^^^^^^^^^^^^^^^^^^ move occurs because value has type `TheDarkKnight`, which does not implement the `Copy` trait

For more information about this error, try `rustc --explain E0507`.
```

``` rust
struct Node<T> {
    point: Box<T>,
}

fn main() {
    let n0 = Node {
        point: Box::new(32),
    };
    let r0 = &n0;
    let b0 = r0.point; // cannot move out of `r0.point` which is behind a shared reference
    println!("{}", b0);
}
```

``` shell
error[E0507]: cannot move out of `r0.point` which is behind a shared reference
  --> src/main.rs:10:14
   |
10 |     let b0 = r0.point;
   |              ^^^^^^^^
   |              |
   |              move occurs because `r0.point` has type `Box<i32>`, which does not implement the `Copy` trait
   |              help: consider borrowing here: `&r0.point`

For more information about this error, try `rustc --explain E0507`.
```

It is fine only if you put something back. \`mem::replace\` can be used
for that:

``` rust
use std::mem;

#[derive(Debug)]
struct Node<T> {
    point: Box<T>,
}

fn main() {
    let mut n0 = Node {
        point: Box::new(32),
    };
    let r0 = &mut n0;
    let b0 = mem::replace(&mut r0.point, Box::new(0));
    println!("{}", b0);
    println!("{:?}", r0);
    println!("{:?}", n0);
}
```

# Ref

-   [References and Borrowing](https://doc.rust-lang.org/book/ch04-02-references-and-borrowing.html)
-   <https://stackoverflow.com/questions/28158738/cannot-move-out-of-borrowed-content-cannot-move-out-of-behind-a-shared-referen>
-   <https://stackoverflow.com/questions/63353762/cannot-move-out-of-which-is-behind-a-mutable-reference>
-   <https://stackoverflow.com/questions/28034646/cant-borrow-file-from-mut-self-error-msg-cannot-move-out-of-borrowed-content>
-   <https://stackoverflow.com/questions/28008585/changing-a-node-in-a-tree-in-rust>
