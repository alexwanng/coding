# Ownership and moves

Because variables are in charge of freeing their own resources, 
**resources can only have one owner**. This also prevents resources 
from being freed more than once. **Note that not all variables own** 
**resources** (e.g. [references]).

> reference 变量没有resource



When doing assignments (`let x = y`) or passing function arguments by value
(`foo(x)`), the *ownership* of the resources is transferred. In Rust-speak, 
this is known as a *move*.

> move 动作有： 
>
> let x = y 赋值
>
> 函数传值 by value



After moving resources, the previous owner can no longer be used. This avoids
creating dangling pointers.

```rust,editable
// This function takes ownership of the heap allocated memory
fn destroy_box(c: Box<i32>) {
    println!("Destroying a box that contains {}", c);

    // `c` is destroyed and the memory freed
}

fn main() {
    // _Stack_ allocated integer
    // integer 这类原始类型都是栈上分配内存 
    let x = 5u32;

    // *Copy* `x` into `y` - no resources are moved
    // Copy 语义的不会发生 move， 就是做了一份copy， 安全
    let y = x;

    // Both values can be independently used
    // Copy后是安全独立的
    println!("x is {}, and y is {}", x, y);

    // `a` is a pointer to a _heap_ allocated integer
    let a = Box::new(5i32);

    println!("a contains: {}", a);

    // *Move* `a` into `b`
    let b = a;
    // The pointer address of `a` is copied (not the data) into `b`.
    // Both are now pointers to the same heap allocated data, but
    // `b` now owns it.
    // a 的 指针地址copy给了b， 它们指向相同的堆上数据， 但是b拥有所有权
    
    // Error! `a` can no longer access the data, because it no longer owns the
    // heap memory
    //println!("a contains: {}", a);
    // TODO ^ Try uncommenting this line
    // 不能够继续通过a访问数据， 因为a不再拥有数据的所有权了

    // This function takes ownership of the heap allocated memory from `b`
    destroy_box(b); //按值传递， 就是move， 相当于被销毁。 

    // Since the heap memory has been freed at this point, this action would
    // result in dereferencing freed memory, but it's forbidden by the compiler
    // Error! Same reason as the previous Error
    //println!("b contains: {}", b);
    // TODO ^ Try uncommenting this line
}
```

[references]: ../flow_control/match/destructuring/destructure_pointers.md
