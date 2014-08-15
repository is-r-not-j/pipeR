

# pipeR

[![Build Status](https://travis-ci.org/renkun-ken/pipeR.png?branch=master)](https://travis-ci.org/renkun-ken/pipeR)

High-performance pipeline operator and object based on a set of simple and intuitive rules, making command chaining definite, readable and fast.

## What's new in 0.4?

- **Major API Change**: `%>>%` operator now handles all pipeline mechanisms and other operators are deprecated.
- Add `Pipe` object that supports object-based pipeline operation.

[Release notes](https://github.com/renkun-ken/pipeR/releases)

## Installation

Install from CRAN:

```r
install.packages("pipeR")
```

Install the development version from GitHub (`devtools` package is required):

```r
devtools::install_github("pipeR","renkun-ken")
```

## Usage

### `%>>%`

If `%>>%` is followed by

* a function name or call like `f` or `f(...)`, pipe to first argument as `f(x)` or `f(x,...)`;
* an expression enclosed within `{}` or `()`, pipe to `.` symbol;
* a lambda expression like `(x -> expr)` or `(x ~ expr)`, pipe to lambda symbol, in this case, `x`.

Here is a cheat-sheet:

```r
x %>>% f            # f(x)
x %>>% f(...)       # f(x,...)
x %>>% { f(.) }     # f(x)
x %>>% ( f(.) )     # f(x)
x %>>% (i -> f(i))  # f(x)
x %>>% (i ~ f(i))   # f(x)
```

### `Pipe()`

`Pipe()` creates a Pipe object that supports light-weight chaining without any external operator. Typically, start with `Pipe()` and end with `$value` or `[]` to extract the final value of the Pipe.

Working with [dplyr](https://github.com/hadley/dplyr):

```r
library(dplyr)
library(pipeR)
Pipe(mtcars)$
  filter(mpg >= mean(mpg))$
  select(mpg, wt, qsec)$
  fun(lm(mpg ~ ., data = .))$
  summary()$
  value
```

Working with [ggvis](https://github.com/rstudio/ggvis):

```r
library(ggvis)
library(pipeR)
Pipe(mtcars)$
  ggvis(~ mpg, ~ wt)$
  layer_points() []
```

Here is a cheat-sheet:

```r
Pipe(x)$foo()$bar()         # build Pipe object for chaining
Pipe(x)$foo()$bar()$value   # extract the final value
Pipe(x)$foo()$bar() []      # extract the final value
Pipe(x)$fun(expr)           # pipe to "."
Pipe(x)$fun(x -> expr)      # pipe to "x"
Pipe(x)$fun(x ~ expr)       # pipe to "x"
```

## Examples

### `%>>%`

Pipe as first-argument to a function:

```r
rnorm(100,mean=10) %>>%
  log %>>%
  diff %>>%
  plot(col="red")
```

Pipe as `.` to an expression enclosed by `{}` or `()`:

```r
rnorm(100) %>>% {
  x <- .[1:50]
  y <- .[51:100]
  lm(y ~ x)
}
```

```r
mtcars %>>%
  (lm(mpg ~ ., data = .))
```

Pipe by lambda expression enclosed by `()`:

```r
mtcars %>>%
  (df -> lm(mpg ~ ., data = df))

mtcars %>>%
  (df ~ lm(mpg ~ ., data = df))
```

### `Pipe()`

`Pipe()` creates a Pipe object. 

- `$` chains functions by first-argument piping and always returns a Pipe object.
- `fun()` evaluates an expression with `.` or by lambda expression.
- `$value` or `[]` extracts the final value of the Pipe object.

Pipe as first-argument to a function:

```r
Pipe(rnorm(100,mean=10))$
  log()$
  diff()$
  plot(col="red") []
```

```r
Pipe(1:10)$
  fun(x -> x + rnorm(1))$
  mean() []
```

## Performance

[Benchmark tests](http://cran.r-project.org/web/packages/pipeR/vignettes/Performance.html) show that pipeR operator and Pipe object can achieve high performance especially when they are intensively called.

- If you do not care about the performance of intensive calling and need heuristic distinction between different piping mechanisms, you may use `%>%` in [magrittr](https://github.com/smbache/magrittr) which also provides additional aliases of basic functions. 
- If you care about performance issues and are sure which type of piping you are using, pipeR can be a helpful choice.

## Vignettes

The package also provides the following vignettes:

- [Introduction](http://cran.r-project.org/web/packages/pipeR/vignettes/Introduction.html)
- [Examples](http://cran.r-project.org/web/packages/pipeR/vignettes/Examples.html)
- [Performance](http://cran.r-project.org/web/packages/pipeR/vignettes/Performance.html)


## Help overview

```r
help(package = pipeR)
```

## License

This package is under [MIT License](http://opensource.org/licenses/MIT).
