---
layout: post
title: "Unit Testing in R"
tags: Programming
---

I have recently started using unit testing after reading a great [post](http://r-pkgs.had.co.nz/tests.html) by Hadley Wickham about their importance and implementation using the `testthat` package. Unit testing is the process of testing small 'units' of code (or functions) and determining if they are behavior appropriately. Prior to unit testing, my code would usually have groups of statements manually going through conditions looking for mistakes, such as;

```r
vector = c("Hello world how are you?", "Input must be a character vector.")
string = "This is a test example"
word = "Problem?"
ReverseString(word, "word")
ReverseString(string, "word")
ReverseString(vector, "word")
ReverseString(word, "char")
ReverseString(string, "char")
ReverseString(vector, "char")
```

There are a few benefits to writing unit tests over just manually testing blocks of code;

1. **Ease of refactoring**: Once the unit tests have been written it is easy to test altered versions of existing code by rerunning the test.
2. **Time**: The unit tests can be run easily and repeatedly during development without the hassle associated with manually checking each condition.
3. **Reproducibility**: Automating the validation of the code improves the reproducibility during development by preventing human based errors, such as forgetting to test a condition or clear the environment before testing.
4. **Documentation**: The development of unit tests encourages proper documentation of the expected behavior of the code.
5. **Organized development**: The simple act of thinking about doing unit tests changes the way a problem is approached (breaking problems into small enough pieces for testing) and encourages thinking about use cases during development.

When writing unit tests, there are a few important things to keep in mind;

1. **Independence**: It is best to keep each test case as independent from the others as possible in order to make errors/failures interpretable when they occur.
2. **Automation**: While it is not required for unit tests to be automated or run from a source file, there are a number of advantages to it. This prevents objects created in the console from changing the behavior of the code and makes it easier to run a series of tests quickly.

## Introduction to Writing a Unit Test

With the package `testthat` writing unit tests are quite simple in R. Once you have installed and loaded the package, tests can be created through a series of expectation statements about the output of a unit of code.

```r
library(testthat)
expect_equal(length(1:10),10)
```
When the test runs correctly there is no output in the console. If the test is not successfu then some useful information will be returned about the failure. For example, for the following expression;

```r
expect_identical("Hello","Hell")
```

We get this error message;

```r
Error: "Hello" not identical to "Hell".
1/1 mismatches
x[1]: "Hello"
y[1]: "Hell"
```

This error message is pretty helpful, but as the complexity of the function and unit tests increases it will be useful to have more explicit information about the condition being checked and the inputs being used. This is particularly useful when using variables for inputs (useful for loops) as the error message uses the variable name rather than value, and the comparison only tells us how the output differs. In order to see this we replace the inputs with variables, and use an incorrect expectation to view the error message.

```r
a = " hello "
b = "hello "
expect_identical(trimws(a),b)
```

```r
Error: trimws(a) not identical to b.
1/1 mismatches
x[1]: "hello"
y[1]: "hello "
```

In order to make the error message we can change the labels for the two conditions being compared, as well as add some information about what the expectation was aiming to test.

```r
expect_identical(trimws(a),b,
                 label = "Trim whitespace from ' hello '", # Label for input
                 expected.label = "'hello '"               # Label for expected output
                 info = "Test correct output trimming whitespace from a string")
```

```r
Error: Trim whitespace from ' hello ' not identical to 'hello '.
1/1 mismatches
x[1]: "hello"
y[1]: "hello "
Test correct output trimming whitespace from a string
```

There are many expectation statements available in `testthat`, allowing you to be highly flexible in the output that you are testing. A few of the ones that I have found myself using are;

1. `expect_equal()` = Use when testing for numerical equality, as it accounts for floating point errors.
2. `expect_equivalent()` = Tests for equality between all elements, but allows for differences between the attributes of the objects (row names, column names, etc).
3. `expect_identical()` = Tests for equality of elements and attributes.
4. `expect_true()` = Test for boolean logic.
5. `expect_error()` = Test for an error, useful for testing proper input validation.

## Testing a Single Function

This is all well and good, but it more helpful to see a more realistic example. A great place to find well written unit tests are the githubs of some of the tidyverse packages such as `stringr`, where you can see production quality code and [testing](https://github.com/tidyverse/stringr/tree/master/tests). For this post I am going to use a simple function I wrote that reverses strings. This function has one user set option (whether to reverse by word or character) and removes all heading and trailing white space.

```r
ReverseString <- function(string, by = c("word","char")) {
    library(magrittr)
    if(!is.vector(string)){stop("Input must be a character vector/string.")}
    if(!is.character(string)|length(string)==0){
        stop("Input must be a non-empty character.")
    }
    if(!by %in% c("word","char")){stop("By must be 'word' or 'char'.")}
    if(by=="char") {split = NULL; collapse = ""}
        else {split = " "; collapse = " "}
    string %>%
        trimws %>%
        strsplit(split) %>%
        lapply(rev) %>%
        sapply(paste0, collapse = collapse) %>%
        return
}
```

The first step of writing a unit test is identifying what it is that you want to test. In this case, I have chosen to check that the output is the correct length across a combination of input cases (single string or vector of strings) and user set options (reverse by word or character). There are two lengths that we are interested in; first that the output vector is of the correct size, and second that the number of characters in the output string is correct.

We can test these conditions through repeating the code above for each of the inputs we are interested in;

```r
expect_equal(length(ReverseString('Hello world','word'),1),
             info = "Single Word Input, Reverse by Word: Check output length")
expect_equal(length(ReverseString('Hello world','char'),1),
             info = "Single Word Input, Reverse by Character: Check output length")
...
```

However, it would be better to combine these tests, are they are investigating different conditions with the same purpose; to check that the output length is correct. We can use the `test_that()` function to do this. `test_that()` allows you to combine expectations under a single label;

```r
test_that("Correct Length", {
    expect_equal(length(ReverseString('Hello world','word'),1),
                 info = "Single Word Input, Reverse by Word: Check output length")
    expect_equal(length(ReverseString('Hello world','char'),1),
                 info = "Single Word Input, Reverse by Character: Check output length")
    ...
})
```

Finally, since all operations we are interested in are expect_equal for checking length, we can throw all of the conditions into a loop.

```r
test_that("Correct Length",{
    # Inputs
    s1 = "Hello world"; s2 = "Goodbye world"
    input = list(word.el.len = length(ReverseString(s1,"word")),
                 char.el.len = length(ReverseString(s1,"char")),
                 word.vec.len = length(ReverseString(c(s1,s1),"word")),
                 char.vec.len = length(ReverseString(c(s1,s1),"word")),
                 word.el.charlen = nchar(ReverseString(s1,"word")),
                 char.el.charlen = nchar(ReverseString(s1,"char")),
                 word.vec.charlen = sapply(c(s1,s2),function(x){
                     nchar(ReverseString(x,by="word"))},USE.NAMES = FALSE),
                 char.vec.charlen = sapply(c(s1,s2),function(x){
                     nchar(ReverseString(x,by="word"))},USE.NAMES = FALSE))
    output = list(word.el.len= 1,
                  char.el.len = 1,
                  word.vec.len = 2,
                  char.vec.len = 2,
                  word.el.charlen = 11,
                  char.el.charlen = 11,
                  word.vec.charlen = c(11,13),
                  char.vec.charlen = c(11,13))
    info = c("Single Word Input, Reverse by Word: Check output length",
              "Single Word Input, Reverse by Character: Check output length",
              "Vector Input, Reverse by Word: Check output length",
              "Vector Input, Reverse by Character: Check output length",
              "Single Word Input, Reverse by Word: Check number of characters",
              "Single Word Input, Reverse by Character: Check number of characters",
              "Vector Input, Reverse by Word: Check number of characters",
              "Vector Input, Reverse by Character: Check number of characters")
    # Testing
    for(i in 1:length(output)){
        expect_equal(input[i],output[i],info=info[i])
    }
})
```

## Integrating into a Project

The example above only tests that the output lengths are correct; in most cases there will be other characteristics that we will want to change depending on our needs (Correct computation time, input validation, formatting of output, etc) and multiple units being tested. Rather than running each in their own file we can automate the process, which in turn makes it easier to log output into files rather than just the console (a topic for another post). For example, say we have two functions (`ReverseString()` and `RotateArray()`) each with a few of their own tests consolidated in their own respective files, */testing/test-ReverseString.R* and */testing/test-RotateArray.R*. `testthat` allows for testing a all unit tests within a directory (all files required to begin with 'test-'), or one file at a time.

In order to use this method, we create a file for running the tests, say */testing/run_tests* that will look something like this;

```r
library(testthat)
# Load functions to be tested
source("functions.R")
# Run all tests in directory
test_dir('testing/')
# Run a single file
test_file('testing/test-ReverseString.R')
```

When running multiple tests with `test_dir()` it can be beneficial to add in a `context()` statement above the tests, as this adds in some information seperating each file being run and describes what unit is being tested. For */testing/test-ReverseString.R* this may look something like this;

```r
context("Reverse String")
test_that("Correct Length") {
    ...
}
test_that("Input Validation") {
    ...
}
```

## Final Comments

I have only touched the surface of unit testing in R and the capabilities of the `testthat` package here, but hopefully it will be enough to get those interested to start. Happy testing!
