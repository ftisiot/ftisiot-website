---
title: 'How to use PostgreSQL® SUBSTRING'
date: "2024-01-12T16:10:27+01:00"
url: "/posts/postgresql-substr"
description: "Examples on how to use PostgreSQL SUBSTR"
tldr: "Examples on how to use PostgreSQL SUBSTR"
image: "/images/2024/substr.png"
credit: "ftisiot"
featured_image: "/images/2024/substr-thumbnail.png"
show_reading_time: true
categories:
- PostgreSQL
- substr
---

Need to extract a specific substring out of a text in PostgreSQL®? Read here how!

<!--more-->

PostgreSQL [SUBSTRING](https://www.postgresql.org/docs/current/functions-string.html) allows you to extract a particular string out of a text column.

The text column can be of:
* `character`
* `character varying`
* `text`

As per note in the [documentation](https://www.postgresql.org/docs/current/functions-string.html), all the string functions are declared to accept and return type `text`, all the other types will be converted to it.

{{< code "/components/pglink" >}}

> **Note**: For most use cases, you can also use the `SUBSTR` function, the cases and related syntax are included in the blog

## Use SUBSTRING to extract a string from a specific index

Let's say we have the following three values in the database:
* `I Love Pizza with Mushroom`
* `Pizza is Home`
* `I had Pizza yesterday`

with substring I could:

* Extract from a precise string index, e.g. retrieve all the substring starting from `5th` character until the end of the string:

```sql
SELECT SUBSTRING(mystr from 5)
FROM
    (VALUES('I Love Pizza with Mushroom'),('Pizza is Home'),('I had Pizza yesterday')) as tbl(mystr);
```

With output

```
       substring        
------------------------
 ve Pizza with Mushroom
 a is Home
 d Pizza yesterday
```

> You can use the equivalent `SUBSTR(mystr, 5)` or `SUBSTRING(mystr, 5)` statement instead of the `SUBSTRING(mystr from 5)`


* Extract from a precise string index, e.g. retrieve all the substring starting from `5th` characte of width `3` characters:

```sql
SELECT SUBSTRING(mystr FROM 5 FOR 3)
FROM
    (VALUES('I Love Pizza with Mushroom'),('Pizza is Home'),('I had Pizza yesterday')) as tbl(mystr);
```

With result

```
 substring 
-----------
 ve 
 a i
 d P
```

> You can use the equivalent `SUBSTR(mystr, 5, 3)` or `SUBSTRING(mystr, 5, 3)` statement instead of the `SUBSTRING(mystr from 5 for 3)`

* Extract a precise set of characters from the beginning of the string. E.g. extract the first `3` characters from the string:

```sql
SELECT SUBSTRING(mystr FOR 3)
FROM
    (VALUES('I Love Pizza with Mushroom'),('Pizza is Home'),('I had Pizza yesterday')) as tbl(mystr);
```

With result

```
 I L
 Piz
 I h
```

> You can use the equivalent `SUBSTR(mystr, 1, 3)` or `SUBSTRING(mystr, 1, 3)` statement instead of the `SUBSTRING(mystr for 3)`

## Use POSITION and SUBSTRING to extract a string from a specific word

The examples above assumed the start from the string was a static character number. What about retrieving all the strings after `Pizza`? To do so we need to first locate the `Pizza` substring using `POSITION` and then use the `SUBSTRING` function.

```sql
SELECT SUBSTRING(mystr FROM POSITION('Pizza' IN mystr))
FROM
    (VALUES('I Love Pizza with Mushroom'),('Pizza is Home'),('I had Pizza yesterday')) as tbl(mystr);
```

Results

```
      substring      
---------------------
 Pizza with Mushroom
 Pizza is Home
 Pizza yesterday
```

What if we want only what **follows** `Pizza`? We need to add the length of the `Pizza` string (plus maybe the space if necessary) to the `FROM` position:

```sql
SELECT SUBSTRING(mystr FROM POSITION('Pizza' IN mystr) + LENGTH('Pizza'))
FROM
    (VALUES('I Love Pizza with Mushroom'),('Pizza is Home'),('I had Pizza yesterday')) as tbl(mystr);
```

Result (note the space at the beginning of the string)

```
   substring    
----------------
  with Mushroom
  is Home
  yesterday
```

## Use SUBSTRING to extract a string using regular expressions

Reusing the case before, we could rewrite the extraction using [POSIX regular expressions](https://www.postgresql.org/docs/15/functions-string.html#:~:text=regular%20expression%3B%20see-,Section%C2%A09.7.3,-.)

```sql
SELECT SUBSTRING(mystr FROM 'Pizza.*$')
FROM
    (VALUES('I Love Pizza with Mushroom'),('Pizza is Home'),('I had Pizza yesterday')) as tbl(mystr);
```

The above, instead of using `POSITION` to find the index of the `Pizza` word, it defines it as regular expression. RegEx are are powerful yet [dangerous](https://blog.codinghorror.com/regular-expressions-now-you-have-two-problems/) way to define the pattern you want find in a specific text. In the example above the `Pizza.*$` defined to retrieve everyting starting with `Pizza` and then followed by any character `.*` until the end of the string `$`.

E.g. if I want to find the last word in each sentence I could write

```sql
SELECT SUBSTRING(mystr FROM '[A-Za-z]*$')
FROM
    (VALUES('I Love Pizza with Mushroom'),('Pizza is Home'),('I had Pizza yesterday')) as tbl(mystr);
```

The result is

```
 substring 
-----------
 Mushroom
 Home
 yesterday
```

## Use SUBSTRING to extract a string using SQL regular expressions

Similar to the above, instead of using POSIX, we could use [SQL regular expressions](https://www.postgresql.org/docs/15/functions-matching.html#FUNCTIONS-SIMILARTO-REGEXP).

To get the first word after `Pizza `:

```sql
SELECT SUBSTRING(mystr SIMILAR '%Pizza #"[A-Za-z]*#"%' ESCAPE '#')
FROM
    (VALUES('I Love Pizza with Mushroom'),('Pizza is Home'),('I had Pizza yesterday')) as tbl(mystr);
```

In the above we are using the `%Pizza #"[A-Za-z]*#"%` expression which states:

* `%Pizza`: Anything starting with `Pizza`
* `#"[A-Za-z]*#"`: Print anything that contains single characters in upper or lowercase
* `%` any trailing characters doesn't need to be displayed

Results

```
 substring 
-----------
 with
 is
 yesterday
```

