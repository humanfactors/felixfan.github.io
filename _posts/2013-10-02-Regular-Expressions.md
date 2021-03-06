---
title: R Regular Expressions
layout: post
categories: [RStudy]
tags: [R, RegExp]
image: /figure
---
{% include JB/setup %}

created on 1 Oct 2013
updated on Wed Oct 02 21:39:27 2013

**NOTE:** The examples used in the function 'grep' can be extended to other functions, to limit the space, I will only give one or two examples for other functions.

### 1. Introduction


R supports two regular expression flavors: POSIX 1003.2 and Perl. By default R uses POSIX extended regular expressions.

* fixed = TRUE: use exact matching.
* perl = TRUE: use Perl-style regular expressions.
* fixed = FALSE, perl = FALSE: use POSIX 1003.2 extended regular expressions.

Regular expressions are represented as strings. Metacharacters often need to be escaped. For example, the metacharacter `"\n"` must be entered as `"\\n"` to prevent R from interpreting the leading backslash before sending the string to the regular expression parser.

### 2. Function 'grep'

```
grep(pattern, x, ignore.case = FALSE, perl = FALSE, value = FALSE, fixed = FALSE, useBytes = FALSE, invert = FALSE)
```

The **grep** function requires two arguments. The first is a string containing a regular expression. The second is a vector of strings to search for matches. The grep function returns a list of indicies of the elements of x that yielded a match.

#### 2.1 Example with dafault parameters


```r
grep("apple", c("I love apple and apple pie", "Apple ipad", "apple ipod"))
```

```
[1] 1 3
```


**Note:** grep is case-sensitive by default.

#### 2.2 Example with case-insensitive match

To perform a case-insensitive match, add ignore.case = TRUE to the function call.


```r
grep("apple", c("I love apple and apple pie", "Apple ipad", "apple ipod"), ignore.case = TRUE)
```

```
[1] 1 2 3
```


#### 2.3 Example return the actual matches

To return the actual matches rather than their indices, add value = TRUE to the function call.


```r
grep("apple", c("I love apple and apple pie", "Apple ipad", "apple ipod"), value = TRUE)
```

```
[1] "I love apple and apple pie" "apple ipod"
```


#### 2.4 Examples with metacharacters

A list of metacharacters: `$ * + . ? [ ] ^ { } | ( ) \`
* `$`: Force the regular expression to be at the end of the string
* `^`: Force the regular expression to be at the beginning of the string
* `*`: The preceding item will be matched zero or more times.
* `+`: The preceding item will be matched one or more times.
* `?`: The preceding item is optional and will be matched at most once
* `{n}`: The preceding item is matched exactly ‘n’ times.
* `{n,}`: The preceding item is matched ‘n’ or more times.
* `{n,m}`: The preceding item is matched at least ‘n’ times, but not more than ‘m’ times.
* `.`: Stands for any character.
* `|`: Alternation match.
* `[ABC]`: means A,B or C.
* `[A-Z]`: means any upper letter between A and Z.
* `[0-9]`: means any digit between 0 and 9.
* `\\d`: Digit, 0,1,2 ... 9
* `\\D`: Not Digit
* `\\s`: Space
* `\\S`: Not Space
* `\\w`: Word
* `\\W`: Not Word
* `\\t`: Tab
* `\\n`: New line

pattern:  any character at the beginning of the string, followed by 'pple':


```r
grep("^.p{2}le", c("I love apple and apple pie", "Apple ipad", "apple ipod"),
value = TRUE)
```

```
[1] "Apple ipad" "apple ipod"
```


pattern: 'p' before 'd', and any character in between


```r
grep("p.d", c("I love apple and apple pie", "Apple ipad", "apple ipod"), value = TRUE)
```

```
[1] "Apple ipad" "apple ipod"
```


pattern: any upper letter


```r
grep("[A-Z]", c("I love apple and apple pie", "Apple ipad", "apple ipod"), value = TRUE)
```

```
[1] "I love apple and apple pie" "Apple ipad"
```


pattern: any letter


```r
grep("[A-Za-z]", c("I love apple and apple pie", "Apple ipad", "apple ipod"),
value = TRUE)
```

```
[1] "I love apple and apple pie" "Apple ipad"
[3] "apple ipod"
```


pattern: pad or pod


```r
grep("pad|pod", c("I love apple and apple pie", "Apple ipad", "apple ipod"),
value = TRUE)
```

```
[1] "Apple ipad" "apple ipod"
```

```r
grep("p(a|o)d", c("I love apple and apple pie", "Apple ipad", "apple ipod"),
value = TRUE)
```

```
[1] "Apple ipad" "apple ipod"
```


### 3. Function 'grepl'

```
grepl(pattern, x, ignore.case = FALSE, perl = FALSE, fixed = FALSE, useBytes = FALSE)
```

The **grepl** function takes the same arguments as the **grep** function, except for the value argument, which is not supported. **grepl** returns a logical vector with the same length as the input vector.


```r
grepl("apple", c("I love apple and apple pie", "Apple ipad", "apple ipod"))
```

```
[1]  TRUE FALSE  TRUE
```


### 4. Function 'regexpr' and 'gregexpr'

```
regexpr(pattern, text, ignore.case = FALSE, perl = FALSE, fixed = FALSE, useBytes = FALSE)
```

```
gregexpr(pattern, text, ignore.case = FALSE, perl = FALSE, fixed = FALSE, useBytes = FALSE)
```

The function **regexpr** and **gregeexpr** requires two arguments: a regular expression and a vector of text to process. Function **regexpr** returns the locations of the regular expression matches. Function **gregexpr** returns the number of matches in each component.


```r
regexpr("apple", c("I love apple and apple pie", "Apple ipad", "apple ipod"))
```

```
[1]  8 -1  1
attr(,"match.length")
[1]  5 -1  5
attr(,"useBytes")
[1] TRUE
```


**regexpr** returns an integer vector with the same length as the input vector. Each element in the returned vector indicates the character position in each corresponding string element in the input vector at which the (first) regex match was found. A match at the start of the string is indicated with character position 1. If the regex could not find a match in a certain string, its corresponding element in the result vector is -1. The returned vector also has a *match.length* attribute. This is another integer vector with the number of characters in the (first) regex match in each string, or -1 for strings that didn't match.


```r
gregexpr("apple", c("I love apple and apple pie", "Apple ipad", "apple ipod"))
```

```
[[1]]
[1]  8 18
attr(,"match.length")
[1] 5 5
attr(,"useBytes")
[1] TRUE

[[2]]
[1] -1
attr(,"match.length")
[1] -1
attr(,"useBytes")
[1] TRUE

[[3]]
[1] 1
attr(,"match.length")
[1] 5
attr(,"useBytes")
[1] TRUE
```


**gregexpr** is the same as **regexpr**, except that it finds all matches in each string.

### 5. Function 'regmatches'

```
regmatches(x, m, invert = FALSE)
regmatches(x, m, invert = FALSE) <- value
```

Use **regmatches** to get the actual substrings matched by the regular expression or replace matched substrings.


```r
x <- c("I love apple and apple pie", "Apple ipad", "apple ipod")
m <- regexpr("apple", x)
regmatches(x, m)
```

```
[1] "apple" "apple"
```



```r
x <- c("I love apple and apple pie", "Apple ipad", "apple ipod")
m <- regexpr("apple", x)
regmatches(x, m) <- "orange"
x
```

```
[1] "I love orange and apple pie" "Apple ipad"
[3] "orange ipod"
```


### 6. Function 'sub' and 'gsub'

```
sub(pattern, replacement, x, ignore.case = FALSE, perl = FALSE,fixed = FALSE, useBytes = FALSE)
```

```
gsub(pattern, replacement, x, ignore.case = FALSE, perl = FALSE,fixed = FALSE, useBytes = FALSE)
```

The function **sub** and **gsub** replace one pattern with another. They requires three arguemtns: a regular expression, a replacement pattern, and a vector of strings to process. **sub** is analogous to `s///` in **Perl**, which replaces only the first instance of a regular expression. **gsub** function is analogous to `s///g` in **Perl**, which replaces all instances of a pattern.


```r
x <- c("I love apple and apple pie", "Apple ipad", "apple ipod")
x
```

```
[1] "I love apple and apple pie" "Apple ipad"
[3] "apple ipod"
```

```r
x <- sub("apple", "orange", x)
x
```

```
[1] "I love orange and apple pie" "Apple ipad"
[3] "orange ipod"
```



```r
x <- c("I love apple and apple pie", "Apple ipad", "apple ipod")
x
```

```
[1] "I love apple and apple pie" "Apple ipad"
[3] "apple ipod"
```

```r
x <- gsub("apple", "orange", x)
x
```

```
[1] "I love orange and orange pie" "Apple ipad"
[3] "orange ipod"
```


### 7. Function 'strsplit'

```
strsplit(x, split, fixed = FALSE, perl = FALSE, useBytes = FALSE)
```

Function **strsplit** splits its input according to a specified regular expression.


```r
x <- c("I love apple and apple pie", "Apple ipad", "apple ipod")
strsplit(x, split = "\\W")
```

```
[[1]]
[1] "I"     "love"  "apple" "and"   "apple" "pie"

[[2]]
[1] "Apple" "ipad"

[[3]]
[1] "apple" "ipod"
```

```r
strsplit(x, split = "")
```

```
[[1]]
[1] "I" " " "l" "o" "v" "e" " " "a" "p" "p" "l" "e" " " "a" "n" "d" " "
[18] "a" "p" "p" "l" "e" " " "p" "i" "e"

[[2]]
[1] "A" "p" "p" "l" "e" " " "i" "p" "a" "d"

[[3]]
[1] "a" "p" "p" "l" "e" " " "i" "p" "o" "d"
```


### 8. Summary

| Function | Purpose |
|:------------- |:-------------|
| grep() | returns a vector of indices where a pattern is matched |
| grepl() | returns a logical vector (TRUE/FALSE) for each element of the data |
| regexpr() | returns an integer vector giving the starting position of the first match, along with a match.length attribute giving the length of the matched text. |
| gregexpr() | returns an integer vector giving the starting position of the all matches, along with a match.length attribute giving the length of the matched text. |
| regmatches() | Extract or Replace Matched Substrings. |
| sub() | replaces one pattern with another at first matching location |
| gsub() | replaces one pattern with another at every matching location |
| strsplit() | breaks apart strings at predefined points |

### 9. References          

[Regular expressions in R](http://www.johndcook.com/r_language_regex.html)          
[Regular Expressions with The R Language](http://www.regular-expressions.info/rlanguage.html)         
[http://en.wikibooks.org/wiki/R_Programming/Text_Processing](http://en.wikibooks.org/wiki/R_Programming/Text_Processing)        
[R Regular Expression](http://www.endmemo.com/program/R/grep.php)         
