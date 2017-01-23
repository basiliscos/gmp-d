# gmp-d

D-language high-level wrapper for [GNU MP (GMP) library](https://gmplib.org/)
that aims to be compatible with `std.bigint.BigInt` (copy construction excluded)
and `@safe pure nothrow @nogc` except when converting to `string`.

Implementation is optimized through

- mapping of GMP's C macros into D inline functions that operate directly on the
  internal C-representation `__mpz_struct`,

- passing of `MpZ`-typed parameters as `auto ref const`. This enables clever
  reuse of `mpz_t` instances when passed to `__gmpz`-functions. Note that D's
  `__traits(isRef)` currently cannot be used to distinguish l-value from r-value
  passing of `this` (it should). This severly limits the possibilities of using
  C++-style
  [expression templates](https://en.wikipedia.org/wiki/Expression_templates) to
  realize lazy evaluation in operator overloading. If this limitation were to be
  fixed the compiler could lower expression such `base^^exp % modulo` to the
  builtin `__gmpz_powm(base, expr, modulo)`. See the unittests for `MpzAddExpr`,
  `MpzMulExpr`, etc for details on how should be implmemeted.

Copy construction is currently disabled for now. Instead use `move(z)` (from
`std.algorithm.mutation`) to pass by move or `z.dup` property if duplication is
needed.

There are more `mpz_t` functions that could be wrapped but these are good start.
