- Feature Name: Deprecate env::set_var
- Start Date: 2015-10-25
- RFC PR: (leave this empty)
- Rust Issue: (leave this empty)

# Summary

`env::set_var` should be deprecated because it is unsafe on Linux.

# Motivation

https://github.com/rust-lang/rust/pull/24741 helpfully marked up
`env::set_var` with some documentation noting why it is unsafe...
but didn't actually mark it unsafe.  At this point, we can't change
a function from safe to unsafe, so our only option is to deprecate
it.

# Detailed design

The existence of `env::set_var` has substantial drawbacks:

- It's difficult to get right in the standard library itself: currently,
spawning a process using standard library functions can crash if `set_var` is
called at the same time.
- Binding substantial C libraries becomes difficult: any C library
which calls any environment-sensitive function (any locale function, `execvp`,
etc.) cannot be bound to Rust without marking up the binding with `unsafe`.

If we deprecate it, and eventually get rid of it, those drawbacks disappear.

# Drawbacks

Depecating a stable function is generally bad for people relying on it, so
we shouldn't do it lightly... but I think it's justified in this case.

# Alternatives

We could ignore the backward-compatibity rules and mark `env::set_var` unsafe.
Bad for obvious reasons.

We could add a new function, marked unsafe, which is equivalent to `env::set_var`.
Given that `set_var` isn't particularly useful in the first place, probably
not worthwhile.

We could make `set_var` modify some Rust-local cache of the environment, and not
the C environment.  This would be perfectly safe, but not really backward-compatible,
and not intuitive for anyone mixing Rust and C.

We could decide that the current warning in the documentation is sufficient, and
do nothing.

# Unresolved questions

None.
