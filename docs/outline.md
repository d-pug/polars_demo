# Polars

## Good

* Very fast. Most notably around reading data, but also operations. Compare
  reading a large CSV with Polars versus Pandas.

* No quirks (as of yet). Nothing like Pandas' SettingWithCopyWarning. Columns
  are columns (no special "indexes"). No `.loc` vs `.iloc` vs `[ ]`.

* Early errors, strictness, and type safety.

* Easy to select columns in many different ways. Want all the numeric columns?
  It's easy. Want all of the columns that end with `_name`? Also easy.

* Compatible with Python's `datetime` module.

* Non-experimental string data type.

* Missing values are `null`, which is distinct from `NaN`, for every data type.

* Easy to switch to lazy evaluation when you need efficiency (replace `read_`
  functions with `scan_` functions and add a call to the `.collect` method
  anywhere you want to force evaluation.

* Easy way to improve performance: combine/chain calls and avoid intermediates.


## Bad

* There's no *simple* way to conditionally transform elements of a column if
  the transformation isn't error-free on the entire column.

* No `read_fwf` yet.

* No complex number type yet (because Arrow doesn't have one yet!).

* Some parts of the API feel redundant. For example:
    - `pl.col` and selectors seem like different ways to do similar things
    - `.str.split_exact` and `.str.splitn` do the same thing in slightly
      different ways

* Most types seem to have two different names, an official one (e.g.,
  `Float64`, `String`) and a short one (e.g., `f64`, `str`). It hasn't been a
  problem yet but the docs don't really explain this.

* Some parts of the API don't use Python idioms. Possibly they're using Rust
  idioms instead. For example:
    - `.clone` instead of `.copy` to copy a data frame
    - `.str.strip_chars` instead of `.str.strip`
