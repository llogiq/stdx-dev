# stdx-dev – the missing development batteries of Rust

New to Rust development and don't know how to improve your development?
stdx-dev [has the best tools](#about-stdx-dev).

First, you're going to want to write Rust code using your favorite editor or
IDE, whether that's [emacs], [vi] or [sublime text], [Eclipse] or [IntelliJ],
[Atom] or [VSCode] – or something else entirely. Most tools seem to coalesce
around [racer] and the nascent [RLS] (Rust Language Server). See
[Are we IDE yet?](https://areweideyet.com/) for further information.

[emacs]: https://github.com/rust-lang/rust-mode
[vi]: https://github.com/rust-lang/rust.vim
[sublime Text]: https://github.com/rust-lang/sublime-rust
[Eclipse]: https://rustdt.github.io/
[IntelliJ]: https://plugins.jetbrains.com/idea/plugin/8182-rust
[Atom]: https://github.com/vertexclique/tokamak
[VSCode]: https://github.com/saviorisdead/RustyCode
[racer]: https://github.com/phildawes/racer
[RLS]: https://github.com/jonathandturner/rls

With that out of the way, here are the goods:

|feature                 |crate                    |
|------------------------|-------------------------|
|code style / correctness|[clippy](#clippy)        |
|formatting              |[rustfmt](#rustfmt)      |
|random testing          |[quickcheck](#quickcheck)|
|benchmarks              |[bencher](#bencher)      |
|profiling               |[flame](#flame)          |
|dependency management   |[cargo-edit](#cargo-edit)|

# Clippy

[Crate](https://crates.io/crates/clippy) |
[Repository](https://github.com/Manishearth/rust-clippy) |
[Docs](https://github.com/Manishearth/rust-clippy/wiki) |
[MPL-2.0]

More than 180 lints (at the time of this writing) catch bugs and unfortunate
patterns and give helpful suggestions.

Install and run clippy with:

```sh
rustup install nightly # get a nightly Rust
cargo +nightly install clippy # install clippy, add "-f" to upgrade existing
cargo +nightly clippy # runs clippy on the current project
```

This will likely give you a list of warnings and suggestions. While there
remain some false positives (and negatives), the overall quality is quite good,
and having worked extensively with clippy, I can attest that it sharpens the
newcomer's intuition about what rustic code makes.

Many IDEs can use clippy within their workflow. Also there's an experimental
[rustfix](https://github.com/killercup/rustfix) tool to auto-apply some of the
suggestions (Warning: Will eat your code).

# RustFMT

[Crate](https://crates.io/crates/rustfmt) |
[Repository](https://github.com/rust-lang-nursery/rustfmt) |
[Docs](https://github.com/rust-lang-nursery/rustfmt/blob/master/README.md) |
[MIT] / [Apache-2.0]

This program will format your code according to some "default" style. It is
highly configurable and will only occasionally eat your laundry.

Install with `cargo install rustfmt` (add `-f` to update an existing
installation) and run with `cargo fmt` for a whole project or `rustfmt <file>`
to format a single file. There are multiple modes of operation, e.g. output as
diff. Check out the documentation for further assistance.

# Quickcheck

[Crate](https://crates.io/crates/quickcheck) |
[Repository](https://github.com/burntsushi/quickcheck) |
[Docs](http://burntsushi.net/rustdoc/quickcheck/) |
[MIT] / [Unlicense]

This is a crate you can use to generate a series of random inputs for your
tests and *reduce* the input in case of test failure.

If you have [cargo-edit], you can type `cargo add --dev quickcheck` into a
command line in your crate root. Otherwise, add the following to your
`Cargo.toml` manually:

```toml
[dev-dependencies]
quickcheck = "*"
```

Now you can use quickcheck in your tests! The easiest way is:

```rust
#[macro_use] extern crate quickcheck;

quickcheck! {
    // any predicate of any type that has an `Arbitrary` implementation
    // most default types already have one
    fn check_it(a: usize, b: usize) -> bool {
        a == b || a != b
    }
}
```

# Bencher

[Crate](https://crates.io/crates/bencher) |
[Repository](https://github.com/bluss/bencher) |
[Docs](https://docs.rs/bencher) |
[MIT] / [Apache-2.0]

Rust has an internal benchmarking tool, but it's only available on nightly
behind a feature gate. This package makes benchmarks available on stable.

You'll need the following incantations in your `Cargo.toml`:

```toml
[dev-dependencies]
bencher = "0.1"

# and for each file:
[[bench]]
name = "bench_something"
harness = false
```

Now we can add `src/bench_something.rs`:

```Rust
#[macro_use] extern crate bencher;

fn bench_mul(b: &mut bencher::Bencher) {
    let i = 1;
    b.iter(|| i * i);
}

// set up the benchmarks group
benchmark_group!(bench, bench_mul); // <- can add more benchmarks here
// and run it
benchmark_main!(bench);
```

This can be run with the usual `cargo bench`.

# Flame

[Crate](https://crates.io/crates/flame) |
[Repository](https://github.com/TyOverby/flame) |
[Docs](https://docs.rs/flame) |
[MIT] / [Apache-2.0]

Flame is a library that allows you to stopwatch your code and see the result
rendered as a flame graph. There is also the (unstable, nightly-only) [flamer]
plugin that lets you annotate your crates/modules/items to insert flame trace
points.

[flamer]: https://github.com/llogiq/flamer

Code augmented by `flame` can look like the following:

```Rust
extern crate flame;

fn foo() {
   flame::start("foo");
   let result = ...;
   flame::end("foo);
   return result;
}

fn main() {
   flame::start("main");
   let foo = foo();
   ...
   flame::end("main");
}
```

The resulting flame graph can look like this:

https://raw.githubusercontent.com/TyOverby/flame/master/resources/screenshot.png

# cargo-edit

[Crate](https://crates.io/crates/cargo-edit) |
[Repository](https://github.com/killercup/cargo-edit) |
[Docs](https://github.com/killercup/cargo-edit/blob/master/README.md) |
[MIT] / [Apache-2.0]

This crate gives you the `cargo add`, `cargo rm` and `cargo list` subcommands,
allowing for easy dependency management from the command line. Refer to the
docs for further information.

----

# About stdx-dev

The [stdx](https://github.com/brson/stdx) curated list of crates has some great
crates that you can build on, but it lacks all kinds of crates that make
*development* with Rust even more awesome.

So I'm going to try and list them here. If you find any crate I've missed, file
an issue or a PR. Thank you!

The contents of this repository are licensed under [MIT] / [Apache-2.0], at
your discretion.

[Apache-2.0]: http://www.apache.org/licenses/LICENSE-2.0
[MIT]: http://opensource.org/licenses/MIT
[MPL-2.0]: https://www.mozilla.org/MPL/2.0/
[Unlicense]: http://unlicense.org/
