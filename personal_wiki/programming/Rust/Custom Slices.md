# Implementing Slices for custom types

Found the question from StackOverflow:

## Question

I have this struct:

```rust
struct Test {
    data: Vec<u8>,
}

impl Test {
    fn new() -> Self {
        return Test { data: Vec::new() };
    }
}

let a = Test::new();
// populate something into a...
```

I would like to implement slice for this type, when doing e.g. `&a[2..5]`, it returns a slice pointing to `2..5` of its internal `data`. Is it possible to do this in Rust?

## Answer

You can do that by implementing the [`Index`](https://doc.rust-lang.org/std/ops/trait.Index.html) trait, and bounding the index to [`SliceIndex`](https://doc.rust-lang.org/std/slice/trait.SliceIndex.html):

```rust
struct Test {
    data: Vec<u8>,
}

impl Test {
    fn new(data: Vec<u8>) -> Self {
        Test { data }
    }
}

impl<Idx> std::ops::Index<Idx> for Test
where
    Idx: std::slice::SliceIndex<[u8]>,
{
    type Output = Idx::Output;

    fn index(&self, index: Idx) -> &Self::Output {
        &self.data[index]
    }
}

fn main() {
    let test = Test::new(vec![1, 2, 3]);

    let slice = &test[1..];
    assert_eq!(slice, [2, 3]);

    let first = &test[0];
    assert_eq!(first, &1);
}
```