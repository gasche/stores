# Stores

The OCaml library `stores` offers several implementations of (in-memory)
stores. A store is a data structure that supports allocating, reading, and
writing memory cells (also known as references). Some implementations offer
additional features, such as persistence (the ability to create multiple
children stores and to work on them simultaneously) or semi-persistence (the
ability to create a child store, work on it, then come back to the parent
store). These implementations offer various tradeoffs between features and
performance.

## Installation

To install the latest released version,
type `opam install stores`.

## Documentation

See the [documentation of the latest released
version](http://cambium.inria.fr/~fpottier/stores/doc/stores).
