# A quick note
Rust provides a handy built in testing mechanism through cargo.  
Simply invoking cargo test will run all the tests defined in the project. However, there is an alternative test runner called cargo-nextest which offers a cleaner test result interface and also runs faster.  
You can install it with cargo install cargo-nextest --locked, once installed, cargo nextest run.  
cargo test --> cargo nextest run

## Creating tests
```rust
#[test]
fn my_test_name() { /* ... */ }
```
When cargo test, the my_test_name function will be executed as a test.  
## Failing tests
Rust tests will fail in two situations:  
1. Panicking  
2. Returning an Err result  

```rust
fn main() {
    println!("Hello, world!");
}

#[test]
fn my_test_name() {
    assert!(1+1==2, "message when false"); // expression
    assert_eq!(2, 1+1, "message when not equal"); // expected, actual
    assert_ne!(1+2, 2+2, "message when equal"); // expr1, expr2 
//    panic!("message");
}
```
Since a test needs to panic to fail, the .expect() on the Result and Option types are great for testing.
## ignored
```rust
#[test]
#[ignore]
fn only_runs_with_flags() {
    std::thread::sleep(std::time::Duration::from_secs(10));
    panic!("test failed");
}
```
cargo test -- --ignored

# Unit test
```rust
#[cfg(test)]
mod tests {
    use super::*;
    // test code goes here
}
```

# Integration test
While unit test get stored in the same file as the source code under test, integration tests get stored outside of the source tree for the project.  
Rusts treats a tests directory in the crate root as an integration test directory.