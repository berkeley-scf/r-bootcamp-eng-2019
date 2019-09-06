% R bootcamp, Module 5: Programming
% August 2019, UC Berkeley
% Emily Grabowski



# Loops

In many languages, looping (for loops, while loops, etc.) is one of the main
constructs used to carry out computation. Loops are not emphasized as much in R,
both because they can be slow and because other syntax is often cleaner.

But there are lots of times when using a loop does make sense.

Most of you are probably familiar at least with the basic idea of iterating
through a series of steps. A for loop iterates through a pre-determined number
of iterations, while a while loop iterates until some condition is met. For
loops are more common in R, but while loops can be handy particularly for things
like optimization.

Let's take our use of `aggregate()` from Module 4 and use a for loop instead.


```r
out <- list()
years <- unique(gap$year)
length(out) <- length(years)
names(out) <- years

for (yrIdx in seq_along(years)) {
     # equivalently: for(i in 1:length(years))
     # n.b., seq_along(x) is better than 1:length(x), since it handles the case
     # where the length of an object is 0 or NULL more robustly.
     sub <- subset(gap, gap$year == years[yrIdx])
     # fit regression
     out[[yrIdx]] <- lm(lifeExp ~ log(gdpPercap), data = sub)
}
out[['2007']]
```

```
## 
## Call:
## lm(formula = lifeExp ~ log(gdpPercap), data = sub)
## 
## Coefficients:
##    (Intercept)  log(gdpPercap)  
##          4.950           7.203
```

```r
summary(out[['2007']])
```

```
## 
## Call:
## lm(formula = lifeExp ~ log(gdpPercap), data = sub)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -25.947  -2.661   1.215   4.469  13.115 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      4.9496     3.8577   1.283    0.202    
## log(gdpPercap)   7.2028     0.4423  16.283   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 7.122 on 140 degrees of freedom
## Multiple R-squared:  0.6544,	Adjusted R-squared:  0.652 
## F-statistic: 265.2 on 1 and 140 DF,  p-value: < 2.2e-16
```

```r
summary(out[['1952']])
```

```
## 
## Call:
## lm(formula = lifeExp ~ log(gdpPercap), data = sub)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -28.9571  -5.7319   0.7517   6.5770  13.7361 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)    -17.8457     5.0668  -3.522 0.000578 ***
## log(gdpPercap)   8.8298     0.6626  13.326  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 8.146 on 140 degrees of freedom
## Multiple R-squared:  0.5592,	Adjusted R-squared:  0.556 
## F-statistic: 177.6 on 1 and 140 DF,  p-value: < 2.2e-16
```

The iterations do not have to explicitly be over sequential numbers.


```r
for (yr in years) {
     cat(yr, '\n')
}
```

```
## 1952 
## 1957 
## 1962 
## 1967 
## 1972 
## 1977 
## 1982 
## 1987 
## 1992 
## 1997 
## 2002 
## 2007
```

# While loop

It's not a particularly interesting example, but we can see the `while` loop
syntax in the same example. In this case 


```r
yrIdx <- 1
while (yrIdx <= length(years)) {
     sub <- subset(gap, gap$year == years[yrIdx])
     # fit regression
     out[[yrIdx]] <- lm(lifeExp ~ log(gdpPercap), data = sub)
     yrIdx = yrIdx + 1
}
summary(out[['2007']])
```

```
## 
## Call:
## lm(formula = lifeExp ~ log(gdpPercap), data = sub)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -25.947  -2.661   1.215   4.469  13.115 
## 
## Coefficients:
##                Estimate Std. Error t value Pr(>|t|)    
## (Intercept)      4.9496     3.8577   1.283    0.202    
## log(gdpPercap)   7.2028     0.4423  16.283   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 7.122 on 140 degrees of freedom
## Multiple R-squared:  0.6544,	Adjusted R-squared:  0.652 
## F-statistic: 265.2 on 1 and 140 DF,  p-value: < 2.2e-16
```

# Branching (if-then-else syntax)

We already saw an example of branching in the *for* loop example.

Here's a simple example to illustrate the syntax. Note that the *then* is
implicit.


```r
val <- rnorm(1)
val
```

```
## [1] -1.147096
```

```r
if (val < 0) {
  print("val is negative")
} else {
  print("val is positive")
}
```

```
## [1] "val is negative"
```

We can chain together `if` statements as follows.


```r
val <- rnorm(1)
val
```

```
## [1] -0.1771986
```

```r
if (val < -1) {
  print("val is more than one standard deviation below the mean.")
} else if (abs(val) <= 1) {
  print("val is within one standard deviation of the mean.")
} else {
  print("val is more than one standard deviation above the mean.")
}
```

```
## [1] "val is within one standard deviation of the mean."
```

In general, the `{` brackets are only needed if you have multiple R expressions,
but R will complain when an `else` starts a line of code, so generally using the
`{` is good practice. That said, this _works just fine_:


```r
if (val < 0) print("val is negative") else print("val is positive")
```

```
## [1] "val is negative"
```

# The condition in an if statement

The condition in the if statement cannot be NA or R will give an error. This is
a very common bug.


```r
continents <- unique(gap$continent)
continents  # what happened ?!
```

```
## [1] "Asia"     "Europe"   "Africa"   "Americas" "Oceania"
```

```r
continents <- unique(as.character(gap$continent))
continents <- c('Antarctica', continents)

out <- rep(0, length(continents))
for (i in seq_along(continents)) {
    sub <- gap[gap$continent == continents[i], ]
    if(mean(sub$lifeExp) < 50)
       print(continents[i])
}
```

```
## Error in if (mean(sub$lifeExp) < 50) print(continents[i]): missing value where TRUE/FALSE needed
```

```r
print(i)
```

```
## [1] 1
```

```r
sub <- gap[gap$continent == continents[i], ]
if(mean(sub$lifeExp) < 50) print('here')
```

```
## Error in if (mean(sub$lifeExp) < 50) print("here"): missing value where TRUE/FALSE needed
```

```r
mean(sub$lifeExp) < 50
```

```
## [1] NA
```

An `NA`/`NaN` is the main reason an if statement may fail, because R will
generally convert other values to logical values.

Zero evaluates to `FALSE`, all other numbers evaluate to `TRUE`. In general
strings are not converted to booleans.

A more robust alternative is to use `isTRUE()`:


```r
out <- rep(0, length(continents))
for (i in seq_along(continents)) {
    sub <- gap[gap$continent == continents[i], ]
    if(isTRUE(mean(sub$lifeExp) < 60))
       print(continents[i])
}
```

```
## [1] "Africa"
```


# Flow control: `next` and `break` statements

`next` skips the current evaluation of the loop statements:


```r
continents <- unique(gap2007$continent)
continents[2] <- "Oceania"; continents[5] <- "Europe"  # reorder to illustrate points below
continents
```

```
## [1] "Asia"     "Oceania"  "Africa"   "Americas" "Europe"
```

```r
out <- list(); length(out) <- length(continents); names(out) <- continents

for (i in seq_along(continents)) {
     # equivalently: for(i in 1:length(continents))
     sub <- subset(gap2007, gap2007$continent == continents[i])
     if(sum(!is.na(sub$lifeExp)) > 2) { # don't regress if <= 2 obs
     # fit regression
       out[[i]] <- lm(lifeExp ~ log(gdpPercap), data = sub)
     } else {
       next
     }
}
cat("We got to iteration ", i, " of ", length(continents), " items.\n", sep = "")
```

```
## We got to iteration 5 of 5 items.
```

```r
out[['Oceania']]
```

```
## NULL
```

`break` immediately ends loop evaluation:


```r
out <- list(); length(out) <- length(continents); names(out) <- continents

for (i in seq_along(continents)) {
     # equivalently: for(i in 1:length(continents))
     sub <- subset(gap2007, gap2007$continent == continents[i])
     if(sum(!is.na(sub$lifeExp)) > 2) { # don't regress if <= 2 obs
     # fit regression
       out[[i]] <- lm(lifeExp ~ log(gdpPercap), data = sub)
     } else {
       break
     }
}

cat("We got to iteration ", i, " of ", length(continents), " items.\n", sep = "")
```

```
## We got to iteration 2 of 5 items.
```

Effective use of `next` and `break` can make your `for` (and other) loops both
more robust and efficient (e.g., by skipping cases where computations may fail).

# Functions

Functions are one of the most important constructs in R (and many other
languages). They allow you to modularize your code - encapsulating a set of
repeatable operations as an individual function call.

You should rely heavily on functions rather than having long sets of
expressions in R scripts.

Functions have many important advantages:

- They reduce bugs by avoiding having multiple instances of the same
  functionality.
- They reduce time involved in coding by eliminating redundancy.
- They make for cleaner and more easily-readable code.

A basic goal is writing functions is *modularity*.

In general, a function should

- be fairly short,
- be focused and specific in what it does, and
- be designed so that it can be used in combination with other functions to
  carry out more complicated operations.

# Writing functions

In module 4, we sorted the gap `data.frame`.


```r
ord <- order(gap$year, gap$lifeExp, decreasing = TRUE)
ord[1:5]
```

```
## [1]  804  672  696 1488   72
```

```r
gm_ord <- gap[ord, ]
```

How would we encapsulate that functionality generically so that
we can apply it to other `data.frame`s (or matrices)?


```r
sortByCol <- function(data, col1, col2) {
    # Sorts a matrix or dataframe based on two  columns
    #
    # Args:
    #     data: a dataframe or matrix with at least columns
    #                  and any number of rows
    #     col1: a reference to the column to sort on
    #     col2: a reference to the column to use for ties
    #
    # Returns:
    #     <data> sorted in increasing order by the values
    #     in the first column. Any ties should be broken by values
    #     in the second column. The row pairs should be maintained
    #     in this result

    ord <- order(data[, col1], data[, col2], decreasing=TRUE)
    sorted <- data[ord, ]
    return(sorted)
}

identical(gm_ord, sortByCol(gap, "year", "lifeExp"))
```

```
## [1] TRUE
```

Of course this is somewhat limited in that it is specific to sorting based on
two columns. We'd usually want to extend this to be more general, but it's
usually good to start with something concrete and more limited in generality and
then generalize once you are sure it works.

# Function arguments

R can match arguments by name (when provided) or by position (the fall-back). It
also allows one to specify default values so that the user doesn't have to
explicitly provide all the arguments.


```r
sortByCol <- function(data, col1 = 1, col2 = 2) {
    ord <- order(data[, col1], data[, col2], decreasing=TRUE)
    sorted <- data[ord, ]
    return(sorted)
}
identical(sortByCol(gap, 1, 2), sortByCol(gap))
```

```
## [1] TRUE
```

```r
identical(sortByCol(col2 = 2, data = gap, col1 = 1), sortByCol(gap, 1, 2))
```

```
## [1] TRUE
```

# What is the "..." argument for?

Using `...` as one of the arguments to a function allows a function to pass
along user-provided arguments without specifying explicitly what the user might
provide.

Here's an example of tailoring some plotting specifications that I use a lot. I
generally define `pplot()` in my *.Rprofile* file so it is always available to
me.


```r
pplot <- function(x, y, ...) {
      plot(x, y, pch = 16, cex = 0.6, ...)
}

pplot(gap$gdpPercap, gap$lifeExp,  xlab = 'gdpPercap (log $)',
      ylab = 'life expectancy (years)', log = 'x')
```

![](figure/usedots-1.png)


# Important concepts with R functions

Functions in R return an object. In general, R functions are and should be
designed such that the only effect of the function is through the return value.

**Side effects** are when a function affects the state of the world in
addition to its return value.  Can you think of any side effects that
you saw an R function produce from earlier today?  What about:

- `library()`
- `setwd()`
- `plot()`

Functions in R are (roughly) *pass-by-value* and not *pass-by-reference*. This
means that if you modify an argument inside the function it will not change the
original value outside the function. This protects you from a major potential
source of side effects. (There are exceptions to this rule.)

In actuality, functions in R are *call-by-value*. What this means for our
purposes is that you can pass an input argument in without a copy being made of
it. This saves time and memory. At the time that you modify the input within the
function (if ever), then a copy is made and  the modified input is different
than the original value outside the function.

# Variable scope and global variables

In general functions should not make use of variables from outside the function.
(However, for quick-and-dirty work and in some other circumstances, one may do
this.) This provides modularity and reduces bugs and surprises.

If R can't find a variable that is used in a function based on the function
arguments and variables defined locally in the function, it goes and looks
elsewhere following a set of rules called *lexical scoping*. (This type of
scoping has to do with R's roots (and explains why R is very similar to other
languages for functional programming) - we won't go into details here but
certainly worth looking into as you start using R more.)

Basically this means that it looks for variables relative to where the function
is defined (not relative to where the function is called).

This can get involved, but a couple brief examples illustrate the basic idea.


```r
x <- 2
f <- function(y) {
    return(x + y)
}
f(1)
```

```
## [1] 3
```

```r
g <- function(y) {
  x <- 10
  return(f(y))
}

g(1)
```

```
## [1] 3
```

```r
g <- function(y) {
  f <- function(y) {
     return(x + y)
  }
  x <- 10
  return(f(y))
}

g(1)
```

```
## [1] 11
```

Note that `x` is used as a global variable here, which in general is bad
practice.

# When do I start programming?

> “[W]e wanted users to be able to begin in an interactive environment,
> where they did not consciously think of themselves as programming.
> Then as their needs became clearer and their sophistication increased,
> they should be able **to slide gradually into programming, when the
> language and system aspects would become more important**.”

[John Chambers, “Stages in the Evolution of
S”](http://www.stat.bell-labs.com/S/history.html)

# Key Principles of R

- Everything that exists is an object.
- Everything that happens is a function call.

   [John Chambers' useR!2014
   talk](http://user2014.stat.ucla.edu/files/chambers.pdf)

### What does the 2nd principle mean?

Are arithmetic operations really just functions?

```r
3+2
```

```
## [1] 5
```

```r
'+'(3,2)
```

```
## [1] 5
```

Yes!

And what about indexing?


```r
x <- matrix(runif(100), 10)
x[ , 2]
```

```
##  [1] 0.60447842 0.18049525 0.30612017 0.05086909 0.02415226 0.90246380
##  [7] 0.63462019 0.04738810 0.51430523 0.95900751
```

```r
'['(x , , 2)
```

```
##  [1] 0.60447842 0.18049525 0.30612017 0.05086909 0.02415226 0.90246380
##  [7] 0.63462019 0.04738810 0.51430523 0.95900751
```

Also yes!

This holds more generally - one can investigate and see the same thing in terms
of a `for` loop.

### What does the 1st principle mean?


```r
class(1)
```

```
## [1] "numeric"
```

```r
class(runif)
```

```
## [1] "function"
```

```r
class(function(x) x^2)
```

```
## [1] "function"
```

```r
square <- function(x) x^2
class(square)
```

```
## [1] "function"
```

# Modern Functional Programming in R (Advanced)

Now that we've taken an extensive look at `for` loops and programming with
functions, we can move on from base R to look at more modern and powerful tools
for programming provided by [`purrr`](http://purrr.tidyverse.org/), a core
package in the [`tidyverse`](http://www.tidyverse.org/), a set of modern tools
for "doing data science" in R. `purrr` provides facilities to manipulate data
sets using functions in a "tidy" manner. Using `purrr` requires familiarity with
several other core packages of the `tidyverse`, most notably `dplyr` and
`magrittr`. We won't have time to cover details of how to use `purrr` here, but
we highly recommend you look into these packages as you continuing exploring R.


```r
library(dplyr)
library(purrr)
```

We'll leave you with a simple example of how to use `purrr` to deal with a task
we already looked at with `for` loops. In fact, we'll return to the logistic
regression exercise that we started with.

### A simple example

Recall that we have been fitting regression models for each year. To fit our models across each
of the years separately, we'll first need to put our data in "tidy"
format. It's _really easy_ with some helpful verbs from `dplyr`...


```r
# let's clean up the data set first
gm_tidy <- gap %>%
  split(.$year)
```

Now, we can fit our regression models across each of the years
using `purrr`'s `map`:


```r
gm_lms <- gm_tidy %>%
  map(~lm(lifeExp ~ log(gdpPercap), data = .))
```

What about protecting ourselves against situations where there wasn't
enough variation in the outcome variable (fewer than two non-missing observations in a year).
(It doesn't happen here but can easily happen in other situations.)

So, can `purrr` handle this? *Yes* - in fact, it's really easy. We can use a
verb called `safely` to separate situations where the GLM succeeds from those
where it doesn't. Let's try it out


```r
gm_lms <- gm_tidy %>%
  map(safely(~lm(lifeExp ~ log(gdpPercap), data = .)))
```

_Remark:_ What we accomplish here with `purrr::map` is also easily done using
tools from base R. In fact, using `lapply`, we can evaluate the very same `lm`
formula with our `gap` dataset, albeit without the extra goodies offered by
the pipe (aka `%>%`) syntax and the `safely` convenience, afforded by `magrittr`
and `purrr`, respectively.

Ok. Now, we'll look at the results for one destination, just to get a feel for
the output


```r
gm_lms$`2007`
```

```
## $result
## 
## Call:
## lm(formula = lifeExp ~ log(gdpPercap), data = .)
## 
## Coefficients:
##    (Intercept)  log(gdpPercap)  
##          4.950           7.203  
## 
## 
## $error
## NULL
```

Now that we've seen what we can do with `purrr`, it's important to compare this
modern approach to what is offered in base R. (It's very important to understand
and be comfortable with using tools from base R before adopting those that are
offered as part of the `tidyverse`.) To that end, here are some points of
comparison:

* Is `purrr::map` really different from `lapply`, `sapply`, `vapply`?
  * `purrr` is:
    * Consistent in the type of object returned
    * contains many useful shortcuts and convenience functions (e.g., `safely`)
    * Syntax accommodates more complicated iteration schemes.
  * [l/s/v]`apply`:
    * The type of object returned is _not_ always consistent
    * Has fewer dependencies (implented in base R rather than `tidyverse`)
     * Syntax can be unwieldy for more complex use-cases.

For a full comparison of `purrr` versus base R, consider checking out [this
quite thorough
tutorial](https://jennybc.github.io/purrr-tutorial/bk01_base-functions.html) by
Jenny Bryan.

### Onwards: Readings and References

* A great reference for learning both basic and advanced concepts in using the R
  language for data analysis is the book _R for Data Science_, by Garrett
  Grolemund and Hadley Wickham. An online version is freely available and may be
  accessed [here](http://r4ds.had.co.nz/). In particular, [chapter 21
  ("Iteration")](http://r4ds.had.co.nz/iteration.html) is a great review of much
  of what we have covered in this module.

* [Here](http://purrr.tidyverse.org/) you can explore the documentation website
  for the `purrr` package. It includes details about functionality we did not
  have time to discuss and many useful examples that you can use to go further
  with `purrr`.

* [Here](http://www.tidyverse.org/) you can browse the `tidyverse` documentation
  website. It includes an introduction to the core packages, the philosophy of
  this ecosystem of packages, and much more.

# Breakout

### Basics

1) Write an R function that will take an input vector and set any negative
values in the vector to zero.

### Using the ideas

2) Write an R function that will take an input vector and set any value below a
threshold to be the value of threshold. Optionally, the function should instead
set values above a threshold to the value of the threshold.

3) Augment your function so that it checks that the input is a numeric vector
and return an error if not. (See the help information for `stop()`.)

4) Figure out what `invisible()` does and why it is useful when writing
functions. Use `invisible()` in your function from just above or in the
`sortByCol()` function.

### Advanced

5) Extend the `sortByCol()` function to handle an arbitrary number of columns on
which to do the sorting. You may need to modify the functionality a bit to use
full vectors for the sorting rather than column names.

6) Extend the `sortByCol()` function so that it can take either numeric indices or
character strings indicating columns of the input data or take entire vectors to
use in the sorting of the input data. If information specifying columns is
given, make sure your function carefully checks that the input refers to
legitimate columns and returns an error or warning (see `warning()`) if not.

7) Explore scoping in the following code. Explain why the result is 11 and not 3. Note that `funGenerator()` returns a function as the return object,
consistent with the idea that functions are objects in R. This is an example of
what is called a *closure* in R. Basically, the function contains object(s)
enclosed with and accessible to the function.

```r
funGenerator <- function(x) {
   x <- 10
   g <- function(y) {
      return(x + y)
   }
   return(g)
}

x <- 2
f <- funGenerator()
f(1)
```

```
## [1] 11
```
