# Notes

These are notes about switching from Polars to Pandas (a demo for the [Davis
Python Users Group][dpug]).

[dpug]: https://datalab.ucdavis.edu/davis-python-users-group/

:::{important}
[Click here][data] to access the shared data directory if you want to follow
along with the live demo.

[data]: https://ucdavis.box.com/s/16dsddsmbaxzt3tij3q0gqwb48srtutf
:::

## Why Polars?

Three things about Polars that convinced me to switch:

1. Polars is more efficient than Pandas. I started by using Polars for
   unoptimized interactive work, and the shorter compute times and lower memory
   use were noticeable right away (without benchmarking). Reading files is also
   faster (in a quick IPython `%timeit` benchmark of each package's `read_csv`
   function on a 159 kb file, Polars is more than 2x faster).

2. Polars is strict about types and raises errors early. The error messages
   also tend to be helpful: they print the values or types that caused a
   problem and sometimes even recommend solutions. Strict type checking has
   helped me identify bugs in Pandas code in the past.

3. Polars makes it delightfully easy to select columns with its `pl.col`
   function (or its `pl.selectors` namespace). You can select with names, with
   a regular expression, by type, and combinations of these. You can also
   easily exclude pesky columns that match your pattern with the `.exclude`
   method.

Things I've noticed and like about Polars now that I've used it for a while:

* Generally, Polars isn't quirky. Pandas has lots of quirks: the
  `SettingWithCopyWarning`, at least three different strategies for indexing
  (`[]`, `.iloc`, `.loc`), indexes that are different from ordinary columns,
  and more. Quirks make a package harder to use and harder to teach.

* Polars supports missing values for every type, and they're always represented
  by `null`, which is implicitly cast to/from `None`. Missing values are not
  conflated with `NaN`, and don't go by multiple names across the API.

* Polars has a non-experimental string data type.

* Polars provides a set of types to represent temporal data that mostly
  parallel the classes in Python's `datetime` module, and implicitly casts
  between these.

* Polars provides an intuitive way to "optimize" code: avoid saving
  intermediate results and combine or chain calls as much as possible. This is
  sort of true for Pandas as well, but Pandas sometimes provides options that
  seem like they'd improve efficiency (hi, `inplace`!) but don't.

* Polars makes it relatively easy to switch to lazy evaluation when you need
  more efficiency. Replace calls to `read_` functions with calls to `scan_`
  functions and add a call to `.collect` anywhere you want to force evaluation.


## Why Not Polars?

I've also run into some things I don't like about Polars:

* Transforming a subset of data with Polars is a little tricky. In particular,
  there's no *simple* way to conditionally transform elements of a column if
  the transformation can't be applied to every element in the column without
  errors (but [there's an issue][subset]).

* Polars doesn't support fixed-width files yet (but [there's an issue][fwf]).

* Polars doesn't support complex numbers yet, because [Arrow][] doesn't support
  complex numbers yet (but [there's an issue][complex]).

[subset]: https://github.com/pola-rs/polars/issues/14092
[fwf]: https://github.com/pola-rs/polars/issues/3151
[Arrow]: https://arrow.apache.org/
[complex]: https://github.com/apache/arrow/issues/16264

* Parts of Polars' API feel redundant. For example: `pl.col` and `pl.selectors`
  do almost the same thing; `.str.split_exact` and `.str.splitn` do almost the
  same thing (of course [there's an issue][split]).

* Parts of Polars' API feel non-idiomatic for Python. For example: `.clone`
  instead of `.copy`; `.str.strip_chars` instead of `.str.strip`. Maybe these
  are idiomatic for Rust?

* Most Polars types seem to have two different names: an official one (such as
  `Float64`, `String`) and a short one (such as `f64`, `str`). As far as I can
  tell, the documentation doesn't explain whether the difference ever matters
  or the latter are just abbreviations for convenience.

  [split]: https://github.com/pola-rs/polars/issues/11640
