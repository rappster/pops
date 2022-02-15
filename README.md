
<!-- README.md is generated from README.Rmd. Please edit that file -->
<!-- badges: start -->

[![Lifecycle:
experimental](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://lifecycle.r-lib.org/articles/stages.html#experimental)
[![CRAN
status](https://www.r-pkg.org/badges/version/valid)](https://CRAN.R-project.org/package=valid)
<!-- badges: end -->

## Installation

You can install the development version from
[GitHub](https://github.com/) with:

``` r
# install.packages("remotes")
remotes::install_github("rappster/pops")
```

## What?

Setting and getting custom options/settings for the packages you are
developing.

## Why?

I often (feel like I) need to store package specific “stuff” such as
options/settings. and always came up with custom code in the various
packages I developed -> trying to practice better “DRY”.

## How?

``` r
library(pops)
```

### Initialize package options

In your package’s `.onLoad()` or `.onAttach()` function you can call

``` r
init_package_options(<pkg_name>)
```

For example

``` r
.onLoad <- function(libname, pkgname) {
    options(digits.secs = 3)
    Sys.setenv(TZ = "UTC")
    Sys.setenv(language = "en")

    init_package_options(
      pkg_name = pkgname, 
      values = list(hello = "world", devops_env = "staging")
    )
    invisible(TRUE)
}
```

As this function’s side effects will only materialize when you load an
actual package, let’s try things out in a more direct manner.

If you are calling this **within** a package project (check by calling
`pkg_name()`) then the following will work.

(Otherwise, just set `pkg_name = "test"` in the following call to
`init_package_options`).

``` r
init_package_options(values = list(hello = "world", devops_env = "staging"))
```

This created an “container environment” in your global options. It’s
either empty or in case you specified `values` it will contain those
objects.

``` r
(option_env_name <- options() %>% names() %>% stringr::str_subset("__.__"))
#> [1] "__.__pops"
```

``` r
(option_env <- getOption(option_env_name))
#> <environment: 0x107986848>
```

``` r
option_env %>% ls()
#> [1] "devops_env" "hello"
```

``` r
option_env$hello
#> [1] "world"
```

### Get and set options

There three convenience functions to get and set options/objects within
the option environment

Return option values in a name-value list

``` r
get_options()
#> $devops_env
#> [1] "staging"
#> 
#> $hello
#> [1] "world"
```

``` r
get_options(c("devops_env", "hello"))
#> $devops_env
#> [1] "staging"
#> 
#> $hello
#> [1] "world"
```

``` r
get_options(c("hello"))
#> $hello
#> [1] "world"
```

Return option values

``` r
get_option("hello")
#> [1] "world"
```

Set option values

``` r
set_options(list(devops_env = "dev", foo = "bar"))
```

``` r
get_options()
#> $devops_env
#> [1] "dev"
#> 
#> $foo
#> [1] "bar"
#> 
#> $hello
#> [1] "world"
```
