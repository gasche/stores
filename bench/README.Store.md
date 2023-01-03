Summary of results observed from the store benchmark.

## Raw benchmark

```
$ time BENCH=Raw IMPL=Ref \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=100 dune exec -- ./benchStore.exe
real	0m2.166s

$ time BENCH=Raw IMPL=TransactionalRef \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=100 dune exec -- ./benchStore.exe
real	0m2.335s

$ time BENCH=Raw IMPL=Vector \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=100 dune exec -- ./benchStore.exe
real	0m3.001s

$ time BENCH=Raw IMPL=Map \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=100 dune exec -- ./benchStore.exe

real	0m14.875s
```

We can observe that:

- TransactionalRef adds almost no overhead to the standard
  implementation.

- Vector adds an overhead of around 40%.

- Map adds an overhead of around 7x in this example run with 2^10
  references. (For a run with 2^20 references the overhead is 12x.)


## Transactional overhead

```
$ time BENCH=Raw IMPL=TransactionalRef \
   NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=100 dune exec -- ./benchStore.exe
real	0m2.248s

$ time BENCH=Transactional-raw IMPL=TransactionalRef \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=100 dune exec -- ./benchStore.exe
real	0m2.298s
```

We observe that for the TransactionalRef implementation, the Raw and
Transactional-raw runtimes are similar -- within the measurement
noise. We observed similar results with more write-intensive settings,
for example NCREATE=15 NREAD=10 NWRITE=25 ROUNDS=10.


## Heterogeneous stores using dynamic type representations

If we required a more flexible heterogeneous-store implementation from
the users, certain store implementations (which are not
naturally heterogeneous) would need to use dynamic type
representations to meet our interface requirements -- see the functor
`Store.Heterogeneous` for details.

The MapDyn and VectorDyn modules are the result of wrapping the Map and
Vector implementations under such a dynamic-type-representation layer,
to evaluate the performance overhead of this layer.

```
$ time BENCH=Raw IMPL=Map \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=20 dune exec -- ./bench/benchStore.exe
real	0m2.849s

$ time BENCH=Raw IMPL=MapDyn \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=20 dune exec -- ./bench/benchStore.exe
real	0m2.977s


$ time BENCH=Raw IMPL=Vector \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=100 dune exec -- ./bench/benchStore.exe
real	0m2.715s

$ time BENCH=Raw IMPL=VectorDyn \
  NCREATE=10 NREAD=20 NWRITE=15 ROUNDS=100 dune exec -- ./bench/benchStore.exe
real	0m3.442s
```

We can observe that in both cases, the overhead of dynamic type
representations is fairly small: less than 5% for maps, less than 30%
for Vector. (Vector is faster than Map so the same absolute time gives
a larger slowdown.)
