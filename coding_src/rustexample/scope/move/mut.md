# Mutability

Mutability of data can be changed when ownership is transferred.

```rust,editable
fn main() {
    let immutable_box = Box::new(5u32);

    println!("immutable_box contains {}", immutable_box);

    // Mutability error
    //*immutable_box = 4;

    // *Move* the box, changing the ownership (and mutability)
    // immutable 的变量传递给 mut 变量， 就可以变成修改类型的了
    let mut mutable_box = immutable_box;

    println!("mutable_box contains {}", mutable_box);

    // Modify the contents of the box
    // 
    *mutable_box = 4;

    println!("mutable_box now contains {}", mutable_box);
}
```