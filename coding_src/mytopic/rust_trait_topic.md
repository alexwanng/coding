# Rust trait topic



## Traits from the Ground Up

> from: https://killercup.github.io/presentation-traits-from-the-ground-up



data and behavior:

> 大多数时间你是在处理data and doing some stuff with it.
>
> 

Let's do the stuff

```rust
fn make_true(input: &str) -> String {
  format!("{}!!", input)
}
```

> 一个普通的方法， 为一个字符串末尾加上感叹号



wrap data:

```rust
struct Fact { text: String }

fn make_true(input: &Fact) -> Fact {
  Fact { text: format!("{}!!", input.text) }
}
```

> 方法里返回Data



add behavior to data:

make_true as a method:

```rust
struct Fact { text: String }

impl Fact {
  fn make_true(&self) -> Fact {
    Fact { text: format!("{}!!", self.text) }
  }
}
```

> 直接给Object增加method



```rust
let fact = Fact { text: String::from("Lorem ipsum") };
let true_fact = fact.make_true();
```

> 调用方法



another make_true:

```rust
struct Fact { text: String }

impl Fact {
  fn make_true(&mut self) {
    self.text.push_str("!!");
  }
}
```

> 使用mut引用， 可以直接修改



Or:

```rust
struct Fact { text: String }

impl Fact {
  fn make_true(mut self) -> Fact {
    self.text.push_str("!!");
    self
  }
}
```

> mut move， 修改完后要显式返回self， 否则默认是销毁的.



Let's give it a name:

```rust
trait Truth {
  fn make_true(&self) -> Self
}
```



Now we can implement it:

```rust
impl Truth for Fact {
  fn make_true(&self) -> Self {
    Fact { text: format!("{}!!", self.text) }
  }
}
```

> 为data实现trait中的方法



Implement it for everything!：

```rust
impl Truth for i32 {
  fn make_true(&self) -> Self {
    42
  }
}
```

> 可以给所有的目标type实现某个trait





Abstract over behavior：

```rust
fn print_news<T: Truth>(facts: &[T]) {
  for fact in facts {
    let fact = fact.make_true();
    println!("{}", fact);
  }
}
```

> 参数用trait来限定， 即要求只有符合某类特征的才允许传入



```rust
use std::fmt;

fn print_news<T: Truth + fmt::Display>(facts: &[T]) {
  for fact in facts {
    let fact = fact.make_true();
    println!("{}", fact);
  }
}
```

> T泛型可以要求多个trait， 使用加号 +



also need:

```rust
impl fmt::Display for Fact {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "{}", self.text)
    }
}
```

> Fact还需要实现Display这个trait



where syntax:



```rust
fn print_news<T: Truth + fmt::Display>(facts: &[T]) {}

fn print_news<T>(facts: &[T]) where T: Truth + fmt::Display {}

```

> 使用where语法简化



Using Generics in impls

```rust
impl<T: Debug> Foo for Vec<T> {
  // ...
}
```

> 在impl的时候使用泛型约束， 即规定T是什么样子的 (前面是方法的反省， 这里是在impl上约束)



Associated items：



Associated functions
That’s what we’ve been writing all this time

> 关联函数是什么？



Associated types
specify types you need to refer to that are specific to each impl block

```rust
trait Iterator {
  type Item;
  fn next(&mut self) -> Option<Self::Item>;
}

impl Iterator for FourIntegers {
  type Item = i32;
  fn next(&mut self) -> Option<i32> {}
}
```

> 关联类型的好处是不需要额外的泛型参数， 有点像模板模式
>
> 但是有一个问题， 感觉这里Option<i32>和type Item = i32有点重复的感觉。 



Associated constants：

```rust
trait SuperLog {
  const LABEL: Display;

  fn log(&self, f: &mut fmt::Formatter) -> fmt::Result;
}
```

> 没看懂代码， 这里constant是什么？



Unambiguous function call syntax：

```rust
fact.make_true()

//expands to

<Fact as Truth>::make_true(fact)
```

> 基于trait的方法调用， 实际是展开的， 即  <Data as Trait>:: method(data)



Operators are traits:

a + b expands to std::ops::Add::add(a, b)

because of:

```rust
impl Add<i32> for i32 {
  type Output = i32;
  // ...
}
```



Marker traits:

```rust
trait Fancy {}

impl Fancy for Vec<String> {}
```

> 没有任何方法， 仅仅作为标记， 比如Send和Sync标记也是如此

