# no-panics-whatsoever
Statically assert that a program written in Rust does not panic

This crate is used to statically assert that no panics whatsoever are present in the final program.
If any panics are present and are not optimized out, compiling will produce the following error:
```sh
error: the static assertion that no panics are present has failed
```

Note: this crate will only work when compiling with `no_std`.

## Example:
```rust
#![no_std]
#![no_main]
#![feature(lang_items, test)]

extern crate libc;
extern crate no_panics_whatsoever;

#[lang = "eh_personality"]
extern "C" fn eh_personality() {}

fn foo(a: &[i32]) -> i32 {
    // a.get(0).copied().unwrap_or(1) // Compiles fine!
    a[0] // Fails to compile!
}

#[no_mangle] // ensure that this symbol is called `main` in the output
pub extern fn main(_argc: i32, _argv: *const *const u8) -> i32 {
    foo(core::hint::black_box(&[42]))
}
```