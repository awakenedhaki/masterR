Functions
================

# Table of Contents

1.  [Introduction to Functions in R](#introduction-to-functions-in-r)
2.  [Designing a function](#designing-a-function)
3.  [Writing a function](#writing-a-function)
4.  [Lexical scoping](#lexical-scoping)
5.  [Operators](#operators)

# Introduction to functions in R

Here you will learn how to write functions\!

So what is a function? A function is a defined operation that you
perform on data. You have actually encountered many functions already in
R. Let’s review a couple of them.

``` r
# The mean function returns the mean of its numeric input
mean(1:60)
```

    ## [1] 30.5

``` r
# The sum function returns the sum of its numeric input
sum(1:60)
```

    ## [1] 1830

We may know conceptually what these functions are outputting, but for
now it seems like there is a bit of magic going on here. How does R know
what a mean is? Or, what a sum is? Even more importantly, how did these
functions come to be?

R has this cool function called `search` that output a vector of
characters, where each character is the path for an R object.

``` r
search()
```

    ## [1] ".GlobalEnv"        "package:stats"     "package:graphics" 
    ## [4] "package:grDevices" "package:utils"     "package:datasets" 
    ## [7] "package:methods"   "Autoloads"         "package:base"

This is how R knows what you mean when you ask it to give you the mean
of some data. It sees `mean` and recognizes that `mean` is a function
defined in the base R package. The base R package is imported by default
when you run R. All the R objects we define in this session will be
stored in `.GlobalEnv`. If you want to know where all these packages and
tools can be found in your own computer, use the `searchpaths` function.

Ok, so this is how R knows *where* to find the functions, but how does
it actually compute the mean? Each function will have an underlying
implementation. This implementation will define how your function
behaves.

R is a programming language that has been built from C, Fortran, and R
itself. As you become more advanced, you may want to start investigating
which functions are implemented in what language, and how they are
implemented. For other instances, you may not need to know the
implementation and just think of the operation as if it were magic\!

Let us make our own version of the mean function.

``` r
#' Iteratively compute arithmetic mean
#' 
#' @param x A numeric vector
#' @return The mean of the vector
#' @examples
#' mean2(c(1, 2, 3))
#' mean2(2:40)
mean2 <- function(x) {
  element_sum <- 0
  for (i in x) {
    element_sum <- element_sum + i
  }
  element_sum / length(x)
}

mean2(1:60)
```

    ## [1] 30.5

We will cover the `#'` and `@` later on. For now, just know that they
are helping us *document* our function, so that other people can figure
out what our function does and how to use it.

We should check where `mean2` is stored by using the `environment`
function.

``` r
environment(mean2)
```

    ## <environment: R_GlobalEnv>

Aha\! It is stored in the global environment, as mentioned before.

We see that our `mean2` produces the same output as the base `mean`
function. I wonder… Is there a difference in runtime, the time it takes
to complete the operation?

``` r
# base R mean function
start <- Sys.time()
mean(1:60)
```

    ## [1] 30.5

``` r
end <- Sys.time()
end - start
```

    ## Time difference of 0.00157094 secs

``` r
# Our mean function
start <- Sys.time()
mean2(1:60)
```

    ## [1] 30.5

``` r
end <- Sys.time()
end - start
```

    ## Time difference of 0.001629829 secs

The difference seems miniscule\! If you call these functions multiple
times, you will see that our function is sometimes faster than base R’s.

How well does our function *scale*?

``` r
start <- Sys.time()
mean(1:60000000)
```

    ## [1] 3e+07

``` r
end <- Sys.time()
end - start
```

    ## Time difference of 0.3205788 secs

``` r
start <- Sys.time()
mean2(1:60000000)
```

    ## [1] 3e+07

``` r
end <- Sys.time()
end - start
```

    ## Time difference of 2.055863 secs

Oof\! This time around our input is 6 orders of magnitude greater that
our first test. We begin to see a massive difference. Our `mean2` takes
2 seconds to do the job… base R has us beat with a whopping 0.3
seconds\! The difference is going back to how these functions are
implemented, and in what language.

In reality, calculating the runtime of functions can be a very
complicated ordeal. If you are interested, read into Big O notation. Our
`mean2` function runs in O(n) time, where n is the length of the given
vector. Essentially, the longer the list, the longer it will take to
compute the mean.

Lets see if we can find our `mean2` in `.GlobalEnv`. To do this, we pass
`mean2` through the `print` function and specify we want the
`.GlobalEnv` environment.

``` r
print(mean2, envir=.GlobalEnv)
```

    ## function(x) {
    ##   element_sum <- 0
    ##   for (i in x) {
    ##     element_sum <- element_sum + i
    ##   }
    ##   element_sum / length(x)
    ## }
    ## <bytecode: 0x7f8cf494ed58>

R gives us the implementation of `mean2`. It also gives us `bytecode:
...`, all this is telling it where the function can be found in memory,
a *pointer*. You can forget about this for now.

# Designing a function

I already showed you a how to write the implementation of a function
with out `mean2` example. This time I will deconstruct my thought
process.

My method of approach to write a function is *data driven* and *test
driven*. In essence, I think about what kind of data I am operating on,
and what I want to output. Then, before I write the actual function, I
design tests for it. The tests are suppose to cover the behaviour of my
function.

These are the questions that you should be thinking about when writing a
function:

  - What is a relevant name?
      - It is best if you avoid to give your function a name that is
        already taken, already exists within the *namespace*.
  - What is my functions *signature*?
      - What *type of data* am I operating on?
      - What *type of data* am I outputting?
      - Do I need *additional arguments* in my function?

<!-- end list -->

``` r
#' @param <INPUT> <INPUT-TYPE>
#' @return <OUTPUT-TYPE>
```

  - What is the *purpose* of my function?

<!-- end list -->

``` r
#' This is my purpose
#' @param <INPUT> <INPUT-TYPE>
#' @return <OUTPUT-TYPE>
```

  - What are some *examples*?
      - Of my functions *input*?
      - Of my functions *output*?

<!-- end list -->

``` r
#' @examples
#' <EXAMPLE>
#' ...
#' <Nth-EXAMPLE>
```

  - Do I need to define this function? Maybe there is a function that is
    already out there in base R or in a package that does exactly what
    you need.

<!-- end list -->

``` r
environment(mean)
```

    ## <environment: namespace:base>

  - How should I implement my function?
      - What *conditions* need to be during the execution of my
        function?
      - What *approach* can I take? For example, iterative or recursive.
      - Our function in three steps:
        1.  Get the length of the given vector, using the `length`
            function
        2.  Sum each element of the vector, via a *for-each* loop
        3.  Divide the the sum of the elements by the length of the
            vector
  - How can I *test* my function? Testing is something that is taken for
    granted, but it is important in order to understand the behaviour of
    your function.
      - What are some *edge cases*? An edge case are extremes cases that
        your function may encounter. For example, in `mean2` and edge
        case would be an empty vector.
      - I will let you learn about testing for now, to get your started,
        you can google **unit tests in R**.

# Writing a function

At the end of the day, there are three components to a function.

  - The arguments it takes in

<!-- end list -->

``` r
formals(mean2)
```

    ## $x

  - The body of the function, or the set of rules it uses to generate
    its output

<!-- end list -->

``` r
body(mean2)
```

    ## {
    ##     element_sum <- 0
    ##     for (i in x) {
    ##         element_sum <- element_sum + i
    ##     }
    ##     element_sum/length(x)
    ## }

  - The environment the function will reside in

<!-- end list -->

``` r
environment(mean2)
```

    ## <environment: R_GlobalEnv>

Onces you have figured out your function signature, purpose, examples,
and a relevant name, you are ready to actually get to some coding.

Actually, you need to know one last thing\! When you function finishes
operating, how does it know what to **return**? A function will return
the last line that in the function body or whatever is wrapped around
`return()`. When `return()` is used, it will signify the end of that
function.

## Addition

1.  Name: `addition`
2.  Signature:

<!-- end list -->

``` r
#' @param x Numeric
#' @param y Numeric
#' @return Numeric
```

3.  Purpose:

<!-- end list -->

``` r
#' Adds two numbers
#' @param x Numeric
#' @param y Numeric
#' @return Numeric
```

4.  Examples:

<!-- end list -->

``` r
#' Adds two numbers
#' @param x Numeric
#' @param y Numeric
#' @return Numeric
#' @examples
#' addition(1, 1)
#' addition(1.3, 5.4)
```

Let us create the *stub* of our function.

``` r
addition <- function(x, y) {}
```

We know that the function is going to be called `addition`, we need to
call on `function()`, and pass our arguments `x` and `y`, and then have
a pair of curly braces.

How will we implement this function? Within the curly braces we can use
the `+` operator.

``` r
addition <- function(x, y) {
  x + y
}
```

## Geometric Mean

The geometric mean is obtained by taking the product of each element in
an array and taking the n<sup>th</sup> root.

1.  Name: `geom_mean`
2.  Signature:

<!-- end list -->

``` r
#' @param arr Numeric vector
#' @return Numeric
```

3.  Purpose:

<!-- end list -->

``` r
#' Produce geometric mean of a numeric vector
#' @param arr Numeric vector
#' @return Numeric
```

4.  Examples:

<!-- end list -->

``` r
#' Produce geometric mean of a numeric vector
#' @param arr Numeric vector
#' @return Numeric
#' @examples
#' geom_mean(c(1, 2, 3))
#' geom_mean(1:3)
```

Let’s create the stub.

``` r
geom_mean <- function(arr) {}
```

There are three things that we have to know in order to obtain our
desired output.

1.  The length of our input
2.  The product of all element in out input
3.  The root of the product of all element

Instead of using the `length` function, let’s keep track of the length
ourselves.

``` r
geom_mean <- function(arr) {
  arr_length <- 0   # This is an accumulator
  for (i in arr) {
    arr_length <- arr_length + 1
  }
}
```

To keep track of the length, we can use an **accumulator**.
Conceptually, there are different types of accumulators. They will
differ in the role that they play within the function. `arr_length`
could be considered a context preserving accumulator, giving that it
tells us how long the array is.

We must now keep track of the product of all elements.

``` r
geom_mean <- function(arr) {
  arr_length <- 0   # This is an accumulator
  product <- 1      # This is another accumulator
  for (i in arr) {
    arr_length <- arr_length + 1
    product <- product * i
  }
}
```

We can use another accumulator that keeps track of our *results so far*.
Notice that this time the accumulator began at `1`. This is important
because we are going to multiple `product` time we loop on our array. If
we start `product` with 0, our end result would be… 0.

We must now get the root of our product.

``` r
geom_mean <- function(arr) {
  arr_length <- 0   # This is an accumulator
  product <- 1      # This is another accumulator
  for (i in arr) {
    arr_length <- arr_length + 1
    product <- product * i
  }
  return(nthroot(product, arr_length))
}
```

We encountered an issue. There is not `nthroot` function in R.

We should make our own.

1.  Name: `nthroot`
2.  Signature:

<!-- end list -->

``` r
#' @param x Numeric
#' @param n Numeric
```

3.  Purpose:

<!-- end list -->

``` r
#' Produce the nth root of a x
#' @param x Numeric
#' @param n Numeric
```

4.  Exmaples:

<!-- end list -->

``` r
#' Produce the nth root of a x
#' @param x Numeric
#' @param n Numeric
#' @examples
#' nthroot(100, 2)
#' nthroot(-30, 5)
```

When we do the square root of a number, it is the same as taking that
said number and raising it to the power of 1/2. Therefore, we will
assume that is true for the nth root.

``` r
nthroot <- function(x, n) {
  return(x ^ (1/n))
}
```

# Lexical scoping

What on earth is *lexical scoping*? This is imply a fancy way of saying,
where can R see my variables?

``` r
x <- 4

some_function <- function() {
  x <- x + 4
  x
}
```

What is the scope of variable `x`? We see it inside and outside of
`some_function`. Within `some_function` we are adding `4` to `x`.
Intuitively, we can assume that `some_function` will call the `x` that
was defined outside of the function, an we would be correct.

However, when `4` is added, does that change maintained *globally*? In
other words, will x equal 8 after the `some_function` call ends?

``` r
x
```

    ## [1] 4

``` r
some_function()
```

    ## [1] 8

``` r
x
```

    ## [1] 4

Looks like the change we made on `x` was restricted to the *local*
environment of our function.

What about the following instance?

``` r
x <- 4

some_function <- function(x) {
  print(x)
  x + 4
}
```

Now `some_function` has `x` as an argument. It is important to note that
the `x` argumnet is not a specific value. As you may remember in math
classes, `x` represents any possible numeric value.

``` r
x
```

    ## [1] 4

``` r
some_function(1)
```

    ## [1] 1

    ## [1] 5

``` r
x
```

    ## [1] 4

`1` is assigned as the value of `x`, **within** the body of the
function. Then `4` is added to the locally defined `x` and returned. No
changes have been made globally, and no global variable was used within
that function.

``` r
x <- 4

some_function <- function(y) {
  print(y)
  y + x
}
```

``` r
x
```

    ## [1] 4

``` r
some_function(2)
```

    ## [1] 2

    ## [1] 6

This time we pass `2` as the value of `y`, and we are given the addition
of `y` and `x`. R knew to use the `x` that is globally defined.

**KEY CONCEPT**: R will look for a variable within the *curly braces*
`{}`, if it can’t find the variable within the first `{}`, R will go to
the next level until it reaches the global environment. If the variable
that R is looking for is not defined anywhere, then R will return an
error. This will also apply for any object in R.

You can check if an object exists using the `exists` function.

``` r
exists("x")
```

    ## [1] TRUE

``` r
exists("mean2")
```

    ## [1] TRUE

# Operators

This is more of a side note that may become useful later on in your R
studies.

Much like you call the `mean` or `sum` function in R, you also use
operators like `+`. How does R know that `+` means addition?

Well the `+` is a function with its own implementation\! The operation
that `+` performs is defined by the **objects** it is operating on.

Numeric objects in R have an *add* method that lets R know to add them.

``` r
1 + 1
```

    ## [1] 2

``` r
`+`(1, 1)
```

    ## [1] 2

However, R does not know what to do when you give it two characters as
the operands of `+`.

``` r
"a" + "b"
```

    ## Error in "a" + "b": non-numeric argument to binary operator

R begins to scream at you\! However, in other languages such an
operation would make sense. For example in python, **strings**, what we
call characters in R, can be operated on by `+`.

``` python
# This is python code
print("a" + "b")
```

    ## ab

The following is **Python** code, I am using this just to illustrate
that `+` is function that relies on its implementation as any other
function would.

``` python
# This is also python code
class MyString:
  def __init__(self, x):
    self.x = x
  def __repr__(self):
    return self.x
  def __add__(self, other):
    return 2
    
x = MyString("a")
y = MyString("b")

print(x, y)
print("a" + "b")
print(x + y)
```

    ## (a, b)
    ## ab
    ## 2

Ignore the code behind the curtain\! Hopefully this will be
understandable at a conceptual level.

We have simply created two strings and assigned it to `x` and `y`. We
print `x` and `y`, and see that they represent, `__repr__`, the string
object `"a"` and `"b"`. We then add `"a"` and `"b"`, and see an output
of `"ab"`. But when we add our `MyString` versions, we get the value
`2`\! What??? Well the implementation of `+` is defined by the `__add__`
function in **Python**.
