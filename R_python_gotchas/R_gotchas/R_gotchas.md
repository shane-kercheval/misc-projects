R Gotchas
=========

Here are a few idiosyncrasies that have caused me issues. Perhaps I
should've known better.

`all()` & `any()`
-----------------

I frequently use the `all()` and `any()` functions when unit-testing and
spot-checking data while doing data analyses.

Recently, I experienced some odd behavior in a Shiny app I created, but
I had unit tested the very thing that wasn't working properly.

Turns out I expected `all()`/`any()` to work with `NULL` values.. Seemed
reasonable.

A common check i'll perform is something like

    my_dumb_function <- function() { return (c(1, 2)) }

    # make sure the found values match the expected values
    expected_values <- c(1, 2)
    found_values <- my_dumb_function()

    all(expected_values == found_values)

    ## [1] TRUE

or

    # make sure the found values is a subset of the valid values
    valid_values <- c(1, 2, 3)

    all(found_values %in% valid_values)

    ## [1] TRUE

Well, turns out that isn't a good idea.

Because what happens if there is a bug in my function and it returns
`NULL`, which is unexpected?

    my_dumb_function_with_error <- function() { return ( NULL ) }  # returns NULL, because of a bug

    found_values <- my_dumb_function_with_error()

    all(expected_values == found_values)

    ## [1] TRUE

WTF?

    expected_values

    ## [1] 1 2

    found_values

    ## NULL

    expected_values == found_values

    ## logical(0)

    all(expected_values == found_values)  # TRUE???

    ## [1] TRUE

Same with %in%

    valid_values

    ## [1] 1 2 3

    found_values

    ## NULL

    found_values %in% valid_values

    ## logical(0)

    all(found_values %in% valid_values)  # TRUE :(

    ## [1] TRUE

The solution for the `all(expected_values == found_values)` is to use
the `identical(expected_values, found_values)` function. (or
`setequal()` if you don't care about order, or unique values).

    found_values

    ## NULL

    expected_values

    ## [1] 1 2

    all(expected_values == found_values)

    ## [1] TRUE

    identical(expected_values, found_values)

    ## [1] FALSE

But, honestly, I wouldn't expect `all()` or `any()` to simply ignore
`logical(0)` and let you go on your merry way.

There probably is a function to solve
`all(found_values %in% valid_values)` but I didn't find one so I just
created my own.

`==` with different vectors of different length
-----------------------------------------------

    x <- c('a', 'b', 'c')
    y <- rep(x, 2)

    x

    ## [1] "a" "b" "c"

    y

    ## [1] "a" "b" "c" "a" "b" "c"

    x == y

    ## [1] TRUE TRUE TRUE TRUE TRUE TRUE

    y == x

    ## [1] TRUE TRUE TRUE TRUE TRUE TRUE

I get why this works. And this is less of a frustration at the language
(compared with the `all()` / `any()` functions above), than something
that I probably should have realised.

But I've been burned by this as well. Specifically, I had a unit test
where I was ensuring I got the correct values that would populate a
Shiny dropdown list. But in certain scenarios the list would be
duplicated (because I wasn't calling `unique()`), but my unit-test
passed.

Again, `identical()` is the solution.

    identical(x, x)

    ## [1] TRUE

    identical(x, y)

    ## [1] FALSE
