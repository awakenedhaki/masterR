R Tutorial
================
Rodrigo Vallejos

# Table of Contents

1.  [Setting up](#setting-up)
2.  [Importing our data](#importing-our-data)
3.  [First glance](#first-glance)
4.  [Subset](#subsetting)
      - [Position](#subset-by-position)
      - [Row and column](#subset-by-row-and-column)
      - [Arrays](#subset-with-arrays)
      - [Column name (1)](#subset-by-column-name-1)
      - [Column name (2)](#subset-by-column-name-2)
      - [Column name (3)](#subset-by-column-name-3)
5.  [`dplyr`](#dplyr)
      - [Piping](#piping)
      - [`select`](#select)
      - [`filter`](#filter)
      - [`mutate`](#mutate)
      - [`group_by` &
    `summarize`](#group_by-&-summarize)
6.  [Visualization](#visualization)
      - [`ggplot2`](#ggplot2)
7.  [`factor`](#factor)

# Setting up

``` r
is_valid <- require(tidyverse)
```

    ## Loading required package: tidyverse

    ## ── Attaching packages ────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse 1.2.1 ──

    ## ✔ ggplot2 3.0.0     ✔ purrr   0.2.5
    ## ✔ tibble  1.4.2     ✔ dplyr   0.7.6
    ## ✔ tidyr   0.8.1     ✔ stringr 1.3.1
    ## ✔ readr   1.1.1     ✔ forcats 0.3.0

    ## ── Conflicts ───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()

``` r
if (!is_valid) {
  install.packages("tidyverse")
  library(tidyverse)
}
```

The `tidyverse` is a collection of packages, such as `ggplot2` and
`dplyr`, that have multiple tools not offered in base R. The tools
offered by the `tidyverse` will make your projects more streamline. You
can find more information about the `tidyerse` in this
[link](https://www.tidyverse.org/).

When we import the `tidyverse`, you will see a **Conflicts**. This is of
no concern. It is just informing us that certain functions that are
defined in `dplyr` have the same name as functions in base R. The
imported functions will be used.

**SIDE NOTE**: We can import packages through the `library` or the
`require` functions. If a package does not exist, `library` will raise
an error and `require` will produce a `boolean`, or `logical`. This
logical value represents if the import was successful, `TRUE`, or
failed, `FALSE`.

# Importing our data

We are using a toy, fictional, dataset obtained from
[Kaggle](https://www.kaggle.com/carlolepelaars/toy-dataset).
Fortunately, this data is stored as a `.csv`, comma-separated values.
This is a standard format that stores tabular data as text. Each cell,
value, is separated by a comma.

**SIDE NOTE**: There are other ways to store data as text. The use of a
comma could be substituted by `;`, for instance. I encourage you to find
information of *flat files*.

``` r
path <- file.path(".", "toy_dataset.csv")
```

Above is the *path* to our data. Be sure to have downloaded and unzipped
the data from Kaggle in the same directory, folder, as this notebook. We
use the `file.path` function to design a *path* that is independent of
the operating system which we may be using. For example, using the
format of a path from Windows in a Mac would not work. We assign this
constructed path to the `path` variable.

For this case, `file.path` is not that useful, given that the `.csv`
file should be resting within the working directory of this R notebook.

``` r
dat <- read_csv(path)
```

    ## Parsed with column specification:
    ## cols(
    ##   Number = col_integer(),
    ##   City = col_character(),
    ##   Gender = col_character(),
    ##   Age = col_integer(),
    ##   Income = col_double(),
    ##   Illness = col_character()
    ## )

``` r
# This would also work, if in the same directory as notebook
dat <- read_csv("toy_dataset.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   Number = col_integer(),
    ##   City = col_character(),
    ##   Gender = col_character(),
    ##   Age = col_integer(),
    ##   Income = col_double(),
    ##   Illness = col_character()
    ## )

After defining our path, we can call the `read_csv`, a function from the
`readr` package, to parse through our file and store our data as a
`tibble`. A `tibble` is a modern version of a dataframe. The main
differences between a tibble and a dataframe are printing, subsetting,
and recycling. You can find out more information about what that means
by clicking on this
[link](https://cran.r-project.org/web/packages/tibble/vignettes/tibble.html).

It is important to note that base R also has a function to parse through
`.csv` files…

``` r
example1 <- read.csv(path, stringsAsFactors = FALSE)
```

The `read.csv` is a wrapper function around the `read.table` function.
With `read.csv` we would have to specify that the `strings`, or
`characters`, in our `.csv` file, are **not** of `factor` type. We will
explore what a `factor` is later on. There are other reasons to use
`read_csv`, such as speed, but it does not matter for the pursposes of
this introduction.

**SIDE NOTE**: If you are curious to find more information about a
function in R, you can use the `?` operator. In the R console, type
`?read.csv`. You will be directed to the `read.csv` documentation within
RStudio.

# First glance

The first thing you should do when getting a new data set is to start
familiarizing yourself with its content. We can use the `head` or `tail`
functions to look into the first or last few rows of our data.

``` r
head(dat)
```

    ## # A tibble: 6 x 6
    ##   Number City   Sex      Age Income Illness
    ##    <int> <chr>  <chr>  <int>  <dbl> <chr>  
    ## 1      1 Dallas Male      41  40367 No     
    ## 2      2 Dallas Male      54  45084 No     
    ## 3      3 Dallas Male      42  52483 No     
    ## 4      4 Dallas Male      40  40941 No     
    ## 5      5 Dallas Male      46  50289 No     
    ## 6      6 Dallas Female    36  50786 No

Looking at the first or last few rows is good and all, but its still not
enough to get familiar with this data. Another nifty tool is `str`,
which is give you the *structure* of your data.

  - Class of the variable being passed: `tibble`
  - Number of observations: `150000 obs`
  - Number of variables: `6 variables`
  - Column names
  - Data type of each column
  - The first few values of that column

`give.attr = FALSE` tells `str` not to print the attributes. You can
remove that argument from the function call, and the output will include
the attributes. Removing `give.attr = FALSE` displays the attributes
since `give.attr` has a default value of
    `TRUE`.

``` r
str(dat, give.attr = FALSE)
```

    ## Classes 'tbl_df', 'tbl' and 'data.frame':    150000 obs. of  6 variables:
    ##  $ Number : int  1 2 3 4 5 6 7 8 9 10 ...
    ##  $ City   : chr  "Dallas" "Dallas" "Dallas" "Dallas" ...
    ##  $ Sex    : chr  "Male" "Male" "Male" "Male" ...
    ##  $ Age    : int  41 54 42 40 46 36 32 39 51 30 ...
    ##  $ Income : num  40367 45084 52483 40941 50289 ...
    ##  $ Illness: chr  "No" "No" "No" "No" ...

You could also just want the number of observations or variables. You
can call the `dim` function that gives you the *dimensions* of the
inputted dataframe. The first element is the number of rows,
observations, and then the number of columns, variables.

``` r
dim(dat)
```

    ## [1] 150000      6

Finally, you can also look at the names of the columns with the
`colnames` function.

``` r
colnames(dat)
```

    ## [1] "Number"  "City"    "Sex"     "Age"     "Income"  "Illness"

# Subsetting

So far we have managed to import our packages, load our data, and see
its structure. But how can we access the data? There are actually
multiple ways to subset your data, we will go through a couple of
examples.

**DEFINITION**: Subsetting is the extraction of parts from a whole.

You can extract information form your dataframes by specify a row and a
column.

Lets check out the `Age` column.

## Subset by position:

  - Every element in a structure has some position, that can be
    represented numerically.

  - `Number` is the first column, and thus, it is of column position
    `1`. `Age` is the fourth column, and therefore, it is in column
    position `4`.

  - You tell R what positions you want by using squarebrackets, `[]`,
    right after your dataframe.

  - Example: `dataframe_name[<ROW_POSITION>, <COLUMN_POSITION>]`

  - You can get the entire column by not specifying the row position.

<!-- end list -->

``` r
head(dat[, 4])
```

    ## # A tibble: 6 x 1
    ##     Age
    ##   <int>
    ## 1    41
    ## 2    54
    ## 3    42
    ## 4    40
    ## 5    46
    ## 6    36

  - If you do not specify any column position, you will simply get the
    entire row of the dataframe

<!-- end list -->

``` r
dat[2, ]
```

    ## # A tibble: 1 x 6
    ##   Number City   Sex     Age Income Illness
    ##    <int> <chr>  <chr> <int>  <dbl> <chr>  
    ## 1      2 Dallas Male     54  45084 No

## Subset by row and column:

  - To get a specific value from a dataframe, pass in a row position and
    a column position or name.
  - Column name must be in quotes.

<!-- end list -->

``` r
dat[2, 4]
```

    ## # A tibble: 1 x 1
    ##     Age
    ##   <int>
    ## 1    54

``` r
dat[2, "Age"]
```

    ## # A tibble: 1 x 1
    ##     Age
    ##   <int>
    ## 1    54

## Subset with arrays:

  - The `[]` operator can take a number, a name, and an *array* of
    values.
  - If you want to get `Age` and `Income`, you will have to pass an
    array of their positions or names.
  - Column names must be in quotes

<!-- end list -->

``` r
head(dat[, c("Age", "Income")])
```

    ## # A tibble: 6 x 2
    ##     Age Income
    ##   <int>  <dbl>
    ## 1    41  40367
    ## 2    54  45084
    ## 3    42  52483
    ## 4    40  40941
    ## 5    46  50289
    ## 6    36  50786

``` r
head(dat[, c(4, 5)])
```

    ## # A tibble: 6 x 2
    ##     Age Income
    ##   <int>  <dbl>
    ## 1    41  40367
    ## 2    54  45084
    ## 3    42  52483
    ## 4    40  40941
    ## 5    46  50289
    ## 6    36  50786

  - If you want rows 50 to 100, and you are too lazy to right an array
    containing all those number, you can use the `:` operator. `:`
    accepts a start and end value, and creates a sequence.

<!-- end list -->

``` r
1:3
```

    ## [1] 1 2 3

``` r
head(dat[50:100, ])
```

    ## # A tibble: 6 x 6
    ##   Number City   Sex      Age Income Illness
    ##    <int> <chr>  <chr>  <int>  <dbl> <chr>  
    ## 1     50 Dallas Female    56  52218 No     
    ## 2     51 Dallas Female    55  47702 No     
    ## 3     52 Dallas Male      42  62512 No     
    ## 4     53 Dallas Female    58  48655 No     
    ## 5     54 Dallas Male      57  55133 No     
    ## 6     55 Dallas Male      32  39309 No

## Subset by column name 1:

  - If you do not know the numerical position of your column, R can be
    coerced to give you the column by column name

<!-- end list -->

``` r
head(dat[, "Age"])
```

    ## # A tibble: 6 x 1
    ##     Age
    ##   <int>
    ## 1    41
    ## 2    54
    ## 3    42
    ## 4    40
    ## 5    46
    ## 6    36

## Subset by column name 2:

  - If you already know you want the entire column, you can just use the
    `[[]]` operator.
  - Give `[[]]` the name of the column, in quotes.

<!-- end list -->

``` r
head(dat[["Age"]])
```

    ## [1] 41 54 42 40 46 36

## Subset by column name 3:

  - You can also use the `$` to get a column from your dataframe
  - Write the column name after `$`, with **no** quotes.

<!-- end list -->

``` r
head(dat$Age)
```

    ## [1] 41 54 42 40 46 36

You now know seven different ways to get access to data in a dataframe\!
However, you may have noticed some differences in the output. The final
two subsetting methods returns a vector of numbers, while all other
methods have returned a dataframe. Always be aware of what kind of data
type your functions or operations will return. For example, we can
calculate the mean of a vector, but we cannot call the mean function on
a dataframe.

**SIDE NOTE**: A vector in R is an array of values of identical data
type. There are also lists in R, which are array of values, however, the
values can be of differing data types.

``` r
ages <- dat$Age
```

We can perform a logical operation on `ages` and then find the sum of
hits divided by the length of the actual vector.

``` r
sum(ages > 25) / length(ages)
```

    ## [1] 0.9875467

Lets break this down further.

1.  `ages > 25`: This will go element wise through our vector. If an
    element is greater than `25`, it will be labelled as `TRUE`,
    otherwise `FALSE`. Essentially, you are creating a new vector of
    `logical` type.
2.  `sum`: This function is then adding the `logical` vector, where the
    sum is equal to the number of `TRUE`’s in the vector. Adding a list
    of `TRUE` and `FALSE` works because under the hood `TRUE` is just
    `1` and `FALSE` is just `0`.
3.  We then divide, `/`, the result sum by the `length` of cities. As
    the function name suggests, it returns the `length` of a given
    vector.

A simpler way of finding out this proportion is by calling the `mean`
function.

``` r
mean(ages > 25)
```

    ## [1] 0.9875467

**SUGGESTION**: Read through other operators that R has been built-in,
these will be fundamental for any future projects.

**IMPORTANT**: Many of the functions that you will encounter will often
have a rather intuitive name. If you want to the median of your data,
you call the `median` function. For the absolute value, you can call the
`abs` function. The more you practice your R, or other languages, the
better you will become a predicting what the names of these functions
will be.

Can we get the rows of individuals that live in `"Dallas"` and are older
than `25`?

1.  Test *which* elements in `dat$City` column are equal to `"Dallas"`.
2.  Test *which* elements in `dat$Age` column are greater than `25`.
3.  In between those two test, add the `&`, AND, operator. This operator
    will only return `TRUE` if its *operands* are `TRUE`.
4.  **OPTIONAL**: Store this new `logical` vector into a new variable.
5.  Pass the vector through the `<ROW_INDEX>` segment of `[]` on your
    dataframe.

<!-- end list -->

``` r
dallas_and_g25 <- dat$City == "Dallas" & dat$Age > 25
dat[dallas_and_g25, ]
```

    ## # A tibble: 19,456 x 6
    ##    Number City   Sex      Age Income Illness
    ##     <int> <chr>  <chr>  <int>  <dbl> <chr>  
    ##  1      1 Dallas Male      41  40367 No     
    ##  2      2 Dallas Male      54  45084 No     
    ##  3      3 Dallas Male      42  52483 No     
    ##  4      4 Dallas Male      40  40941 No     
    ##  5      5 Dallas Male      46  50289 No     
    ##  6      6 Dallas Female    36  50786 No     
    ##  7      7 Dallas Female    32  33155 No     
    ##  8      8 Dallas Male      39  30914 No     
    ##  9      9 Dallas Male      51  68667 No     
    ## 10     10 Dallas Female    30  50082 No     
    ## # ... with 19,446 more rows

If you did not want the actual data, but just the indices where these
value are stored, you can use the `which` function. This will take in a
`logical` vector, and return a vector with the index value at any point
where the original vector has a `TRUE`.

``` r
tail(which(dallas_and_g25))
```

    ## [1] 19702 19703 19704 19705 19706 19707

You can perform another test, were you are looking for people who live
in `Dallas`, or are older than `25`. The same procedure as above
applied. The different is the logical operator used. This time we want
the `|`, OR, operator. It will only return `FALSE` if its operands are
`FALSE`.

``` r
head(dat$City == "Dallas" | dat$Age > 25)
```

    ## [1] TRUE TRUE TRUE TRUE TRUE TRUE

# `dplyr`

`dplyr` will be one of the most powerful packages in your toolbox. This
package helps with *data wrangling*, or munging, which is the act of
manipulating/transforming your data into a form that is more conducise
for downstsream processing.

## Piping

Before we get into `dplyr` functions, we must first learn about
**piping**.

Piping allows you to pass the output of one function as the input of
another. The pipe operator in R is `%>%`, and it is found in the
`magrittr` package of the `tidyverse`.

Below we are getting the mena of individuals that live in Dallas and are
older than 25. We then get the `Income` column, convert it to the
thousands, and then calculate the mean income. We will cover all the
functions we called to perform this operation ina second. What is
important to take out of this example is how ugly this code
is\!

``` r
summarize(mutate(select(filter(dat, City == "Dallas" & Age > 25), Income), Income = Income/1000), meanIncome = mean(Income))
```

    ## # A tibble: 1 x 1
    ##   meanIncome
    ##        <dbl>
    ## 1       45.3

Piping will allow you to prevent having this degree of function
*nesting*. Let us find a more visually appealing form.

``` r
dat %>%
  filter(City == "Dallas" & Age > 25) %>%
  select(Income) %>%
  mutate(Income = Income / 1000) %>%
  summarize(mean(Income))
```

    ## # A tibble: 1 x 1
    ##   `mean(Income)`
    ##            <dbl>
    ## 1           45.3

This looks more understandable\! We have our `dat` dataframe, and we
slingshot that into the `filter` function. The filtered dataframe, then
goes to `select`, `mutate`, and finally `summarize` lets us take the
mean of the `Income`.

Readability, while very important when writing any sort of code, is not
the only reason to use pipes. I encourage you to look into why we use
pipes, and what other pipe operators are available in R.

The indentation are just to make the code neater. It is easier to read
line by line, rather than having a long line of pipes.

## `select`

`select` is a function that lets us *select* specific columns by name.
This is much like what we have learned earlier with subsetting.

``` r
ages <- select(dat, Age)
```

Lets use our new `%>%` operator.

``` r
ages <- dat %>% select(Age)
ages
```

    ## # A tibble: 150,000 x 1
    ##      Age
    ##    <int>
    ##  1    41
    ##  2    54
    ##  3    42
    ##  4    40
    ##  5    46
    ##  6    36
    ##  7    32
    ##  8    39
    ##  9    51
    ## 10    30
    ## # ... with 149,990 more rows

The pipe operator took `dat` and slingshot it to the `select` function.
Then, `select` extract the `Age` column from `dat`.

## `filter`

As the name implies, `filter` will *filter* out the data you want from a
given input. This is similar to what we did earlier with `"Dallas"` and
`25` in the previous section.

``` r
dallas_and_g25_income <- dat %>% 
  filter(City == "Dallas" & Age > 25) %>%
  select(Income)
dallas_and_g25_income
```

    ## # A tibble: 19,456 x 1
    ##    Income
    ##     <dbl>
    ##  1  40367
    ##  2  45084
    ##  3  52483
    ##  4  40941
    ##  5  50289
    ##  6  50786
    ##  7  33155
    ##  8  30914
    ##  9  68667
    ## 10  50082
    ## # ... with 19,446 more rows

Here we not only *filtered* a rows with `"Dallas"` and greater than
`25`, but we then piped that through to `select` and extracted a
dataframe with the `Income` of those people.

In other words, we managed to *filter* rows from a dataframe based on
some condition, and then *pipe* that output into `select` and *select*
the `Income` column.

## `mutate`

Using `mutate` you can grab a pre-existing column and perform an
operation over it, or create a new column. We will go through two
examples.

On our last example we got a dataframe of `Income`. However, these are
some big numbers\! Lets divide all of them by 1000.

``` r
dallas_and_g25_income <- dat %>% 
  filter(City == "Dallas" & Age > 25) %>%
  select(Income) %>%
  mutate(Income = Income / 1000)
dallas_and_g25_income
```

    ## # A tibble: 19,456 x 1
    ##    Income
    ##     <dbl>
    ##  1   40.4
    ##  2   45.1
    ##  3   52.5
    ##  4   40.9
    ##  5   50.3
    ##  6   50.8
    ##  7   33.2
    ##  8   30.9
    ##  9   68.7
    ## 10   50.1
    ## # ... with 19,446 more rows

Amazing\! Every element in the column has changed to its quotient.
However, we should be more explicit as to what we mean by `Income`. Lets
create new column with these quotients, rather than replacing the
originals.

``` r
dallas_and_g25_income <- dat %>% 
  filter(City == "Dallas" & Age > 25) %>%
  select(Income) %>%
  mutate(Income_thousands = Income/1000)
dallas_and_g25_income
```

    ## # A tibble: 19,456 x 2
    ##    Income Income_thousands
    ##     <dbl>            <dbl>
    ##  1  40367             40.4
    ##  2  45084             45.1
    ##  3  52483             52.5
    ##  4  40941             40.9
    ##  5  50289             50.3
    ##  6  50786             50.8
    ##  7  33155             33.2
    ##  8  30914             30.9
    ##  9  68667             68.7
    ## 10  50082             50.1
    ## # ... with 19,446 more rows

## `group_by` & `summarize`

So far we have learned how to `select`, `filter`, and `mutate` our
dataframes. That is already quite a lot to take in\!

There are two more powerful tools that you should know. Let us go
through an example, and then explain what happened.

``` r
mean_income_by_city <- dat %>% 
  select(City, Income) %>%
  mutate(Income_thousands = Income / 1000) %>%
  group_by(City) %>%
  summarize(mean_Income = mean(Income_thousands))
mean_income_by_city 
```

    ## # A tibble: 8 x 2
    ##   City            mean_Income
    ##   <chr>                 <dbl>
    ## 1 Austin                 90.3
    ## 2 Boston                 91.6
    ## 3 Dallas                 45.3
    ## 4 Los Angeles            95.3
    ## 5 Mountain View         135. 
    ## 6 New York City          96.9
    ## 7 San Diego             101. 
    ## 8 Washington D.C.        71.0

The break down:

1.  Create and assign the final dataframe to variable
    `mean_income_by_city`
2.  *Select* the `City` and `Income` columns
3.  Create new variable `Income_thousands`, and assign it the quotient
    of `Income / 1000`
4.  *Group* the dataframe by their cities
5.  Calculate the mean income per city

You can think of a `City` as a category, and there are people that fall
under that category. We can group all the information about those people
under that `City`. It is like creating a new dataframe for each `City`.

After we group out cities, any operation we perform will be executed on
a per group basis. For example `mutate` operates on a per element basis,
dividing each `Income` value by `1000`. However, when we call the `mean`
function on `Income_thousands`, the operation is performed group wise.

# Visualization

So far we have covered how to import your data, subset it, and transform
it. However, an important part of exploring and communicating your data
is through **visualizations**. A strong visualization can go a long way
in communicating your results.

Hadley Wickham, the Chief Scientist of RStudio and author of the
`tidyverse` packages, said in the OpenVis 2017 conference:

> “Data visualization is fundamentally a human process. You can see
> something in a visualization that you could not expect, and no
> computer program could have told you about. However, since data
> visualization is fundamentally a human process, it will not scale.”

## `ggplot2`

# `factor`
