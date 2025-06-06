The self parameter in a method has an invalid generic "receiver type".

Erroneous code example:

```rust
struct Foo;

impl Foo {
    fn foo<R: std::ops::Deref<Target=Self>>(self: R) {}
}
```

or alternatively,

```rust
struct Foo;

impl Foo {
    fn foo(self: impl std::ops::Deref<Target=Self>) {}
}
```

Methods take a special first parameter, termed self. It's normal to
use self, &self or &mut self, which are syntactic sugar for
self: Self, self: &Self, and self: &mut Self respectively.
But it's also possible to use more sophisticated types of self
parameter, for instance std::rc::Rc<Self>. The set of allowable
Self types is extensible using the nightly feature
[Arbitrary self types][AST].
This will extend the valid set of Self types to anything which implements
std::ops::Deref<Target=Self>, for example Rc<Self>, Box<Self>, or
your own smart pointers that do the same.

However, even with that feature, the self type must be concrete.
Generic self types are not permitted. Specifically, a self type will
be rejected if it is a type parameter defined on the method.

These are OK:

```
struct Foo;

impl Foo {
    fn foo(self) {}
    fn foo2(self: std::rc::Rc<Self>) {} // or some other similar
        // smart pointer if you enable arbitrary self types and
        // the pointer implements Deref<Target=Self>
}
```

[AST]: <https://doc.rust-lang.org/unstable-book/language-features/arbitrary-self-types.html>
