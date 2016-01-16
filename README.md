# rust-fnv

An implementation of the [Fowler–Noll–Vo hash function](chongo).

### [Read the documentation](http://doc.servo.org/fnv/)


## About

The FNV hash function is a custom `Hasher` implementation that is more
efficient for smaller hash keys.

[The Rust FAQ states that](faq) while the default `Hasher` implementation,
SipHash, is good in many cases, it is notably slower than other algorithms
with short keys, such as when you have a map of integers to other values.
In cases like these, [FNV is demonstrably faster](graphs).

Its disadvantages are that it performs badly on larger inputs, and
provides no protection against collision attacks, where a malicious user
can craft specific keys designed to slow a hasher down. Thus, it is
important to profile your program to ensure that you are using small hash
keys, and be certain that your program could not be exposed to malicious
inputs (including being a networked server).

The Rust compiler itself uses FNV, as it is not worried about
denial-of-service attacks, and can assume that its inputs are going to be
small—a perfect use case for FNV.


## Usage

To include this crate in your program, add the following to your `Cargo.toml`:

```toml
[dependencies]
fnv = "1.0.0"
```


## Using FNV in a HashMap

To configure a `HashMap` in the standard library to use the FNV hasher, you
must create a default instance of a `FnvHasher` state, then create a new
map using this state with `HashMap::with_hash_state`. A full example:

```rust
#![feature(hashmap_hasher)]
use std::collections::HashMap;
use std::collections::hash_state::DefaultState;
use fnv::FnvHasher;

let fnv = DefaultState::<FnvHasher>::default();
let mut map = HashMap::with_hash_state(fnv);
map.insert(1, "one");
map.insert(2, "two");
```


## Using FNV in a HashSet

The standard library’s `HashSet` can be configured to use the FNV hasher
with the same mechanism.

```rust
#![feature(hashmap_hasher)]
use std::collections::HashSet;
use std::collections::hash_state::DefaultState;
use fnv::FnvHasher;

let fnv = DefaultState::<FnvHasher>::default();
let mut set = HashSet::with_hash_state(fnv);
set.insert(1);
set.insert(2);
```

[chongo]: http://www.isthe.com/chongo/tech/comp/fnv/index.html
[faq]: https://www.rust-lang.org/faq.html#why-are-rusts-hashmaps-slow
[graphs]: http://cglab.ca/~abeinges/blah/hash-rs/