
<!-- README.md is generated from README.Rmd. Please edit that file -->

[![lifecycle](https://img.shields.io/badge/lifecycle-experimental-orange.svg)](https://www.tidyverse.org/lifecycle/#experimental)
[![Travis build
status](https://travis-ci.org/vegawidget/vegawidget.svg?branch=master)](https://travis-ci.org/vegawidget/vegawidget)
[![CRAN
status](https://www.r-pkg.org/badges/version/vegawidget)](https://cran.r-project.org/package=vegawidget)

# vegawidget

The goal of vegawidget is to render Vega-Lite and Vega specifications
into htmlwidgets. For now, this document serves as a manifesto for what
we might hope for the package.

Vega and Vega-Lite specifications are just text, formatted as JSON.
Let’s build a modest Vega-Lite specification using lists:

``` r
spec_mtcars <-
  list(
    `$schema` = "https://vega.github.io/schema/vega-lite/v2.json",
    description = "An mtcars example.",
    data = list(values = mtcars),
    mark = "point",
    encoding = list(
      x = list(field = "wt", type = "quantitative"),
      y = list(field = "mpg", type = "quantitative"),
      color = list(field = "cyl", type = "nominal")
    )
  )  
```

The `vegawidget()` function is used to render specifications into
htmlwidgets. If you are reading this document on the GitHub code site,
it is further rendered into a PNG:

``` r
library("vegawidget")
#> 
#> Attaching package: 'vegawidget'
#> The following object is masked _by_ '.GlobalEnv':
#> 
#>     spec_mtcars

vegawidget(spec_mtcars)
```

![](man/figures/README-vegawidget-1.png)<!-- -->

If you wish to examine a specification, you may use the `vw_examine()`
function, which is a thin wrapper around the `jsonedit()` function from
the **listviewer** package.

``` r
vw_examine(spec_mtcars)
```

![](man/figures/README-unnamed-chunk-2-1.png)<!-- -->

If you wish to deploy a specification to be rendered on bl.ocks.org, you
can do that too (provied you have a GutHub account).

``` r
# not yet fully implemented
vw_create_block(spec_mtcars)
```

## Installation

You can install vegawidget from github with:

``` r
# install.packages("devtools")
devtools::install_github("vegawidget/vegawidget")
```

## Usage

This package provides functions to render Vega and Vega-Lite
specifications; it does not help you build specifications. This is left
to other packages. Further, this package is designed *not* to be loaded
directly; rather its purpose is to make rendering-functions available
for such other packages to use.

For example, let’s convert `spec_mtcars` to an S3 object with class
`"my_class_name"`:

``` r
spec_mtcars_s3 <- structure(
  spec_mtcars,
  class = c("my_class_name")
)

class(spec_mtcars_s3)
#> [1] "my_class_name"
```

To take full advantage of these rendering functions, we have to define a
method, `as_vegaspec()` for this class, and also define `print()` and
`knit_print()` methods.

``` r
as_vegaspec.my_class_name <- function(spec, ...) {
  
  # reverts to list
  spec <- unclass(spec)

  # as_vegaspec has a list method
  vegawidget::as_vegaspec(spec, ...)
}

print.my_class_name <- function(x, ...) {
  
  x <- vegawidget::as_vegaspec(x)
  
  print(x, ...)
}

knit_print.my_class_name <- function(x, ..., options = NULL){
  
  x <- vegawidget::as_vegaspec(x)

  knitr::knit_print(x, ..., options = options)
}
```

With these defined, you can render your specification by printing:

``` r
spec_mtcars_s3
```

![](man/figures/README-unnamed-chunk-3-1.png)<!-- -->

Accordingly, this package offers a templating function,
`use_vegawidget(class)` (not yet implemented), to help you integrate
vegawidget functions into your package.

## Future work

In the future, challenges will include:

  - defining interactions for **shiny**.
