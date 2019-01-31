Loops
================

# Table of Contents

1.  [While loop](#while-loop)
2.  [For loop](#for-loop)
      - [For-each](#for-each)
      - [For](#for)
3.  [Key words](#key-words)
4.  [`apply`](#apply)

# `while` loop

## Structure

    while (condition) {
        # body of loop
    }

## Overview

A `while` loop will continue executing what is in its body as long as a
condition is met. For a condition to met, it must be evaluated to
`TRUE`.

A common mistake people do is forgetting to update there condition
within the loop. If you do not update your condition, it will continue
evaluating as `TRUE`. You will be stuck in an **infinite loop**.

Where you evaluate your condition within the loop may depend on the
purpose of the loop. Typically, the update will occur as the last line.

``` r
i <- 1
while (i <= 10) {
    print(paste("This is loop number", i))
    i <- i + 1
}
```

    ## [1] "This is loop number 1"
    ## [1] "This is loop number 2"
    ## [1] "This is loop number 3"
    ## [1] "This is loop number 4"
    ## [1] "This is loop number 5"
    ## [1] "This is loop number 6"
    ## [1] "This is loop number 7"
    ## [1] "This is loop number 8"
    ## [1] "This is loop number 9"
    ## [1] "This is loop number 10"

Our `i` object, variable, will be set before the loop begins. The
`while` loop will then evaluate the *relational* expression. If the
condition is evaluated to `TRUE`, then R will access the body of the
loop. The `print` function will execute. Then the `i` variable will be
updated. Once the `i` variable is updated the loop will start for the
top again, and evaluate the condition.

# `for` loop

A for loop will iterate over the elemts that it is provided. There are
to ways to implement for loops.

## `for-each`

### Structure

    for (element in elements) {
        # Body of loop
    }

### Overview

With the `for-each` method, you will iterate from the beginning of the
list, until you reach the end.

You use the `for` key word, and within parethensis you declare a
temporary variable, for example `element`, and the list you are
iterating over, `elements`. Within the body of the loop you will be able
to operate on `element`.

In essence this is very similar to you assigning the first value of
`elements` to a variable `element`. Then you assign the second one, and
the third one, and so on until the end of the list.

Your temporary variable will be updated when the body of the loop is
done executing.

You run no risk of an infinite loop.

## `for`

### Structure

    for (i in seq_along(elements)) {
        # body of loop
    }

### Overview

In this time of for loop you no longer have direct access to the element
in elements. You now have access to each elements *index*.

Look at the documentation for `seq_along` by using the `?` operator.

This function is generating a sequence of integers that correspond to
the relative position of each element in a list. You can use this value
to indirectly gain access to elements.

You may be thinking that you could simply use `1:length(elements)`, and
avoid `seq_along`. The issue with doing this is that without
`seq_along`, if `elements` is empty,you will get the `c(1, 0 )`. If your
loop body is subsetting and empty list with the index value `1`, you
will get `NULL`.

`seq_along` protects you against this, given that it will generate a 0,
rather than `c(1, 0)`.

# Key words

## `break`

The `break` statement will terminate a loop. This can be placed anywhere
within the loop, and assume as it is encountered, R will exit the loop
and won’t come back. You may want to use this for certain conditions
that may be encountered within the loop.

``` r
i <- 0
while (TRUE) {
    if (i > 10) {
        break
    }
    i <- i + 1
}
```

The above while loop’s condition will always be `TRUE`. However, within
its body, we passed a condition that if met, the loop will run into a
`break` statement causing the loop to end.

## `next`

There many be times when you want to perform an operation upto a certain
point. The `next` statement will make the loop skip whatever code has
not been executed in its body, and go to the next iteration.

``` r
i <- 1
while (TRUE) {
    if (i < 5 ) {
        i <- i + 2
        next
    }
    if (i > 0) {
        break
    }
}
```

In the above `while` loop, we use a `break` and `next`. It seems that we
should be exiting the loop within the first iteration, given that we
access `break` if `i > 0`, which it is. However, before that that
condition is executed and evaluated to `TRUE`, the loop evalute another
`if clause`. R find that `i < 5`, and then adds `2` to `i`. The `next`
key word is met, and the next `if` statement is skipped. R will not be
able to access that statement until the above condition is `FALSE`.

For the above example, do not confuse this if an `if-else`. Both `if`
statements would have been evaluted if `next` was not present.

# `apply`

In a seperate segment of this series we will learn about the `apply`
family. However, I think that it important to briefly mention it here.

The `apply` family is a group of abstract function. By *abstract*, I
simply am referring to the fact that it can take in some list object,
but also a function.

Let’s go through an example, comparing the `sapply` function to a
`for-each` loop.

``` r
numbers <- 1:100

# for-each loop
num_chr_loop <- c()
for (num in numbers) {
    num_characters <- append(num_chr_loop, as.character(num))
}

# sapply
num_chr_apply <- sapply(numbers, as.character)
```

In the above code, we have a vector of numbers, from 1 to 100, and we
are trying to convert each number to its character form, `1` to `"1"`.

In the `for-each` loop, we iterate over each number in a vector. We call
the `as.character` function, and append the results to our accumulator,
`num_chr_loop`.

In the `sapply` approach, we pass our numbers vector and the
`as.character` function, and we obtain the same output.

The great thing about using `sapply` is that our code is less crowded.
The `for-each` method has a lot going on, and it strains the eyes.

I will cover more about the `apply` family and also `purrr` functions in
a seperate guide.
