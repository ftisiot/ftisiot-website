---
title: 'How to CONCAT in MySQL'
date: "2024-02-12T14:43:29+01:00"
url: "/posts/mysql-concat"
description: "How to perform string concatenation in MySQL"
tldr: "How to perform string concatenation in MySQL"
image: "/images/2024/concat_mysql.png"
credit: "ftisiot"
featured_image: "/images/2024/concat_mysql.png"
show_reading_time: true
categories:
- MySQL
- Concatenation
- Concat
- String
---


One of the most common tasks with strings is concatenation! This blog post showcases several techniques to perform it with MySQL.

<!--more-->

{{< code "/components/mysqllink" >}}

## String concatenation in MySQL by placing strings next to each other

Let's take a basic example: a table containing `firstname` and `surname` fields we want to concatenate:

```sql
CREATE TABLE CLIENTS (
    id INT PRIMARY KEY, 
    firstname VARCHAR(100), 
    surname VARCHAR(100),
    region VARCHAR(100),
    creation_date date,
    preferred_item VARCHAR(100)
    );
INSERT INTO CLIENTS VALUES 
    (1, 'John', 'Doe', 'EMEA', '2024-01-01', 'football'),
    (2, 'Laura', 'Smith', 'EMEA', '2024-02-01', 'tennis ball'),
    (3, 'Eva', 'Book', 'APAC', '2024-01-03', null),
    (4, 'Andy', 'Novel', 'APAC', '2024-02-05', 'teddy bear'),
    (5, 'Eva', 'Leeds', 'APAC', '2024-02-01', 'football');
```

We can concatenate two quoted strings by placing one string next to the other like:

```sql
select 'MyFirstname' 'MySurname';
```

The result is

```
+----------------------+
| MyFirstname          |
+----------------------+
| MyFirstnameMySurname |
+----------------------+
1 row in set (0.03 sec)
```

We can see that:
* the result is the correct concatenation
* the column name is the first quoted string

However, if we apply the same principle to standard columns instead of quoted strings with:

```sql
select firstname surname from CLIENTS;
```

We get a different result:

```
+---------+
| surname |
+---------+
| John    |
| Laura   |
| Eva     |
| Andy    |
| Eva     |
+---------+
5 rows in set (0.02 sec)
```

The result showcases only the `firstname` in the results, with the `surname` being included as the result column heading.

> TLDR: concatenating strings in MySQL by placing strings next to each other is possible only if we are dealing with quoted strings.

### Concatenating a non string

Is it possible with the same method to concatenate a non string?

```sql
select 'MyFirstname' 1;
```

Nope, we get `ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near '1' at line 1`

### Concatenating a NULL

What about concatenating a `NULL` value?

```sql
select 'MyFirstname' NULL;
```

Same error `ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'NULL' at line 1`

## String concatenation in MySQL with the `||` operator

We can also concatenate `firstname` and `surname` using the `||` operator with:

```sql
select firstname || surname from CLIENTS;
```

The result will be:

```
+-----------------------+
| firstname || surname  |
+-----------------------+
| JohnDoe               |
| LauraSmith            |
| EvaBook               |
| AndyNovel             |
| EvaLeeds              |
+-----------------------+
5 rows in set (0.02 sec)
```

Please check that there's no space between the `firstname` and `surname`, but we can add it with:

```sql
select firstname || ' ' || surname from CLIENTS;
```

Which shows the expected result

```
+------------------------------+
| firstname || ' ' || surname  |
+------------------------------+
| John Doe                     |
| Laura Smith                  |
| Eva Book                     |
| Andy Novel                   |
| Eva Leeds                    |
+------------------------------+
5 rows in set (0.02 sec)
```

### Concatenating a non string

What about concatenating strings with other fields that are not a string? If, for example, we try to concat the `firstname` and `id` (an `integer`) with the following:

```sql
select firstname || id from CLIENTS;
```

We get the expected result

```
+------------------+
| firstname || id  |
+------------------+
| John1            |
| Laura2           |
| Eva3             |
| Andy4            |
| Eva5             |
+------------------+
```

The same if we try to concatenate the `creation_date` date field

```sql
select firstname || creation_date from CLIENTS;
```

Result:

```
+-----------------------------+
| firstname || creation_date  |
+-----------------------------+
| John2024-01-01              |
| Laura2024-02-01             |
| Eva2024-01-03               |
| Andy2024-02-05              |
| Eva2024-02-01               |
+-----------------------------+
5 rows in set (0.02 sec)
```

However we might want to add a function to the date to get alternative formats from the default, for example the `DATE_FORMAT` function below converts the date in a weird format separating years, months and days with `..`

```sql
select firstname || DATE_FORMAT(creation_date, '..%Y..%M..%D') from CLIENTS;
```

Results

```
+---------------------------------------------------------+
| firstname || DATE_FORMAT(creation_date, '..%Y..%M..%D') |
+---------------------------------------------------------+
| John..2024..January..1st                                |
| Laura..2024..February..1st                              |
| Eva..2024..January..3rd                                 |
| Andy..2024..February..5th                               |
| Eva..2024..February..1st                                |
+---------------------------------------------------------+
5 rows in set (0.03 sec)
```

### Concatenating a NULL value

What does it happen if we try to concatenate a `NULL` value? If you check the dataset created, the row with `id=3` for `Eva Book` has a `preferred_item` equal to `NULL`, let's check the output of concatenating the `firstname`, `surname` and `preferred_item`:

```sql
select firstname || surname || preferred_item  from CLIENTS;
```

Result

```
+------------------------------------------+
| firstname || surname || preferred_item   |
+------------------------------------------+
| JohnDoefootball                          |
| LauraSmithtennis ball                    |
| NULL                                     |
| AndyNovelteddy bear                      |
| EvaLeedsfootball                         |
+------------------------------------------+
5 rows in set (0.03 sec)
```

The result shows that the entire concatenation string is `NULL`!

> TLDR: a concatenation will return `NULL` if any of the strings is `NULL`


## String concatenation in MySQL with the `CONCAT` function

Another option is to use MySQL [Concat function](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_concat). Let's try with the basic `firstname` and `surname` example:

```sql
select concat(firstname, surname) from CLIENTS;
```

The results are in line with the previous `||` example, not having any additional separator added into the resulting string.

```
+----------------------------+
| concat(firstname, surname) |
+----------------------------+
| JohnDoe                    |
| LauraSmith                 |
| EvaBook                    |
| AndyNovel                  |
| EvaLeeds                   |
+----------------------------+
5 rows in set (0.03 sec)
```

How can we add a space in between? We have two options:

* Add a space to the `CONCAT` function

```sql
select concat(firstname, ' ' ,surname) from CLIENTS;
```

* Use the `CONCAT_WS` function (concat with separator) passing the space as separator

```sql
select concat_ws(' ', firstname, surname) from CLIENTS;
```

### Concatenating a non string with the CONCAT function

What happens if we include non strings in the `CONCAT` function?

```sql
select concat(firstname, id) from CLIENTS;
```

As before we get the expected result

```
+-----------------------+
| concat(firstname, id) |
+-----------------------+
| John1                 |
| Laura2                |
| Eva3                  |
| Andy4                 |
| Eva5                  |
+-----------------------+
5 rows in set (0.03 sec)
```

### Concatenating a NULL value with the CONCAT function

What about `NULL` values? 


```sql
select concat(firstname, surname, preferred_item)  from CLIENTS;
```

As before, if any of the source strings is `NULL`, the result of the concatenation is `NULL`

```
+--------------------------------------------+
| concat(firstname, surname, preferred_item) |
+--------------------------------------------+
| JohnDoefootball                            |
| LauraSmithtennis ball                      |
| NULL                                       |
| AndyNovelteddy bear                        |
| EvaLeedsfootball                           |
+--------------------------------------------+
5 rows in set (0.03 sec)
```

## String aggregation via concatenation in MySQL with the `GROUP_CONCAT` function

If we need to concatenate all the strings belonging to the same group, we can use MySQL [GROUP_CONCAT](https://dev.mysql.com/doc/refman/8.0/en/aggregate-functions.html#function_group-concat) function.

If, in our example, we want to concatenate the `firstname` of all the people grouped by their `region` we can write the following:

```sql
select region,
    group_concat(firstname)
from
    CLIENTS
group by region
```

The result is the following, with `,` as the default separator:

```
+--------+-------------------------+
| region | group_concat(firstname) |
+--------+-------------------------+
| APAC   | Eva,Andy,Eva            |
| EMEA   | John,Laura              |
+--------+-------------------------+
2 rows in set (0.03 sec)
```

We can also order how the strings are ordered in the list with:

```sql
select region,
    group_concat(firstname ORDER BY firstname)
from
    CLIENTS
group by region;
```

Result

```
+--------+--------------------------------------------+
| region | group_concat(firstname ORDER BY firstname) |
+--------+--------------------------------------------+
| APAC   | Andy,Eva,Eva                               |
| EMEA   | John,Laura                                 |
+--------+--------------------------------------------+
2 rows in set (0.03 sec)
```

And change the separator with

```sql
select region,
    group_concat(firstname ORDER BY firstname SEPARATOR '$$') res
from
    CLIENTS
group by region;
```

Result

```
+--------+----------------+
| region | res            |
+--------+----------------+
| APAC   | Andy$$Eva$$Eva |
| EMEA   | John$$Laura    |
+--------+----------------+
2 rows in set (0.02 sec)

```

We can also only list the distinct values, removing the duplication of `Eva` with:

```sql
select region,
    group_concat(DISTINCT firstname ORDER BY firstname SEPARATOR '$$') res
from
    CLIENTS
group by region;
```

Result

```
+--------+-------------+
| region | res         |
+--------+-------------+
| APAC   | Andy$$Eva   |
| EMEA   | John$$Laura |
+--------+-------------+
2 rows in set (0.03 sec)
```

### Aggregating and concatenating a non string with the GROUP_CONCAT function

Let's try with a non string, like the `id` field:

```sql
select region,
    group_concat(id) res
from
    CLIENTS
group by region;
```

Works!

```
+--------+-------+
| region | res   |
+--------+-------+
| APAC   | 3,4,5 |
| EMEA   | 1,2   |
+--------+-------+
2 rows in set (0.02 sec)
```

The same if we try with `creation_date`

```
+--------+----------------------------------+
| region | res                              |
+--------+----------------------------------+
| APAC   | 2024-01-03,2024-02-05,2024-02-01 |
| EMEA   | 2024-01-01,2024-02-01            |
+--------+----------------------------------+
2 rows in set (0.02 sec)
```

### Aggregating and concatenating a NULL value with the GROUP_CONCAT function

What about our `NULL` value? Let's check it out!

```sql
select region,
    group_concat(preferred_item) res
from
    CLIENTS
group by region;
```

In this case the behaviour is different:

```
+--------+----------------------+
| region | res                  |
+--------+----------------------+
| APAC   | teddy bear,football  |
| EMEA   | football,tennis ball |
+--------+----------------------+
2 rows in set (0.02 sec)
```

Even if the row with `id=3` part of the `APAC` group had `preferred_item` equal to `NULL`, the resulting aggregate is **not NULL**! This is a common behaviour for aggregate functions to ignore the `NULL` values.

## Conclusion

Here's an handy overview of the concatenation possibilities in MySQL:


| Method | code | works with quoted strings | works with standard columns | works with non strings | Works with `NULL` | aggregates results |
|---|---|---|---|---|---|---|
| Placing strings next to each other | 'a' 'b' | ✅ | ❌ | ❌ | ❌ | ❌ |
| &#124; operator | 'a' &#124;&#124; 'b' | ✅ | ✅ | ✅ | ✅ | ❌ | 
| CONCAT function | concat('a','b') | ✅ | ✅ | ✅ | ✅ | ❌ | 
| GROUP_CONCAT function | group_concat('a','b') | ✅ | ✅ | ✅ | ✅ | ✅ | 
