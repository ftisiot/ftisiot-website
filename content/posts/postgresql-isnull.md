---
title: '11 Lessons to learn when using NULLs in PostgreSQL®'
date: "2024-02-28T08:00:29+01:00"
url: "/posts/postgresql-isnull"
description: "11 Lessons to learn when using NULLs in PostgreSQL®"
tldr: "11 Lessons to learn when using NULLs in PostgreSQL®"
image: "/images/2024/isnullgreyed.png"
credit: "ftisiot"
featured_image: "/images/2024/isnull.png"
show_reading_time: true
categories:
- PostgreSQL
- SQL
- null
- isnull
- coalesce
---

A boolean value should only contain two values, `True` or `False`, but is it correct? Usually people assume so, but sometimes miss the fact that there could be the **absence** of the value all-together. In databases this is absence is usually stored as `NULL` and this blog showcases how to find them, use them properly and 11 lessons to learn to be a `NULL` Pro! 

> Keep in mind, it's not only booleans that can contain `NULL` values, it's all the columns where you don't define a `NOT NULL` constraint!

<!--more-->

{{< code "/components/pglink" >}}

## It all starts with some columns and rows

Let's start from the basics: you have a PostgreSQL® database and a table, called `users` like:

```sql
CREATE TABLE users (
    id SERIAL,
    username TEXT PRIMARY KEY,
    name TEXT,
    surname TEXT,
    age INT
);
```

> Hey, do you want to test the above code but not having a PostgreSQL database handy? Past your code in [PostgreSQL Playground](https://pgplayground.com/?utm_medium=organic&utm_source=ext_blog&utm_content=ftisiotNULL) and quickly check the results!

Let's insert some data:

```sql
INSERT INTO users (username, name, surname, age) VALUES
    ('jdoe', 'Jon', 'Doe', 25),
    ('lspencer','Liz', 'Spencer', 35),
    ('hlondon','Hanna', 'London', 45);
```

Querying the data showcases the table with all the columns filled.

```
 id | username | name  | surname | age 
----+----------+-------+---------+-----
  1 | jdoe     | Jon   | Doe     |  25
  2 | lspencer | Liz   | Spencer |  35
  3 | hlondon  | Hanna | London  |  45
(3 rows)
```

## Insert NULLs
Now, let's check if we can insert some `NULL`s, let's try by inserting them in the `name`, `surname` and `age` columns:

```sql
INSERT INTO users (username, name, surname, age) VALUES ('test',NULL, NULL, NULL);
```

This works since we don't have any constraint

```
 id | username | name  | surname | age 
----+----------+-------+---------+-----
  1 | jdoe     | Jon   | Doe     |  25
  2 | lspencer | Liz   | Spencer |  35
  3 | hlondon  | Hanna | London  |  45
  4 | test     |       |         |    
(4 rows)
```

We can even simplify the insert with the same effect

```sql
INSERT INTO users (username) VALUES ('test1');
```

Result:

```
 id | username | name  | surname | age 
----+----------+-------+---------+-----
  1 | jdoe     | Jon   | Doe     |  25
  2 | lspencer | Liz   | Spencer |  35
  3 | hlondon  | Hanna | London  |  45
  4 | test     |       |         |    
  5 | test1    |       |         |    
(5 rows)
```

## Inserting NULLs 

The first step to avoid `NULL`s from appearing in a table is to forbid inserting them. The section below showcases a few situations on how to avoid inserting `NULL`s in new and existing columns.

### Inserting NULLs in the primary key

Can I insert a `NULL` in the table primary key? In theory this could be allowed since there's no explicit `NOT NULL` constraint on the column. Let's try:

```sql
INSERT INTO users (username) VALUES (NULL);
```

However this fails with:

```
ERROR:  null value in column "username" of relation "users" violates not-null constraint
DETAIL:  Failing row contains (6, null, null, null, null).
```

**💡 Lesson 1 💡**: Primary Keys are by default `NOT NULL`

### Create a new column with NOT NULL constraint

Let's add a new column to our table:

```sql
ALTER TABLE users ADD COLUMN points INT NOT NULL;
```

This will fail, since the previously inserted data don't have any associated value for `points`

```
ERROR:  column "points" of relation "users" contains null values
```

Let's add a default value with:

```sql
ALTER TABLE users ADD COLUMN points INT DEFAULT 0 NOT NULL;
```

The table was altered, and now contains `0 points` for all the existing rows.

```
 id | username | name  | surname | age | points 
----+----------+-------+---------+-----+--------
  1 | jdoe     | Jon   | Doe     |  25 |      0
  2 | lspencer | Liz   | Spencer |  35 |      0
  3 | hlondon  | Hanna | London  |  45 |      0
  4 | test     |       |         |     |      0
  5 | test1    |       |         |     |      0
(5 rows)
```

If we try to insert a new row without specifying the `points` column:

```sql
INSERT INTO users (username) VALUES ('usrwithnullpoints');
```

The row is inserted with the default `0 points`

```
 id |     username      | name  | surname | age | points 
----+-------------------+-------+---------+-----+--------
  1 | jdoe              | Jon   | Doe     |  25 |      0
  2 | lspencer          | Liz   | Spencer |  35 |      0
  3 | hlondon           | Hanna | London  |  45 |      0
  4 | test              |       |         |     |      0
  5 | test1             |       |         |     |      0
  7 | usrwithnullpoints |       |         |     |      0
(6 rows)
```

What if we try to push a `NULL` into `points`?

```sql
INSERT INTO users (username, points) VALUES ('forcenullpoints', NULL);
```

We get the `ERROR:  null value in column "points" of relation "users" violates not-null constraint` stating that our insert is violating the `NOT NULL` constraint.

**💡 Lesson 2 💡**: Setting a `NOT NULL` constraint doesn't allow inserting `NULL`s

**💡 Lesson 3 💡**: If we want to add a `NOT NULL` column to an existing table with data, we need to specify the **default** value for existing rows.

### Add a NOT NULL constraint to an existing column

If we try to change an existing column (e.g. `name`) to add the `NOT NULL` constraint:

```sql
ALTER TABLE users ALTER COLUMN name SET NOT NULL;
```

We get the similar error `ERROR:  column "name" of relation "users" contains null values`, therefore we need also need to amend the current `NULL` values (and possibly set a default)

```sql
ALTER TABLE users 
    ALTER COLUMN name TYPE TEXT USING (COALESCE(name, 'Hugo')),
    ALTER COLUMN name SET DEFAULT 'Hugo',
    ALTER COLUMN name SET NOT NULL
    ;
```

In the above we: 
* Altered the type, setting it to `TEXT` again, to apply the function `COALESCE(name, 'Hugo')` which is replacing `NULL`s in the `name` field with `Hugo`
* Set the default value for the `name` column to `Hugo` <- This is not strictly necessary
* Applied the `NOT NULL` constraint since we don't have any `NULL` values anymore

> Note: the above method locks the entire table until done. A faster approach could be to split the `COALESCE(name, 'Hugo')` into a dedicated update and then only perform the `NOT NULL` and `DEFAULT` settings on the `ALTER TABLE` statement.

The results are in line with the expected, no `NULL`s in the `name` column, being replaced by `Hugo`

```
 id |     username      | name  | surname | age | points 
----+-------------------+-------+---------+-----+--------
  1 | jdoe              | Jon   | Doe     |  25 |      0
  2 | lspencer          | Liz   | Spencer |  35 |      0
  3 | hlondon           | Hanna | London  |  45 |      0
  4 | test              | Hugo  |         |     |      0
  5 | test1             | Hugo  |         |     |      0
  7 | usrwithnullpoints | Hugo  |         |     |      0
(6 rows)
```

**💡 Lesson 4 💡**: If we need to add a `NOT NULL` column to an existing column in a table, we need to amend the existing `NULL` values and optionally set the **default** value for incoming rows.

What if I want to **remove** the `NOT NULL` constraint? We can do it with:

```sql
ALTER TABLE users 
    ALTER COLUMN name DROP NOT NULL
    ;
```

### Is DEFAULT enough?

What if we leave the `NOT NULL` constraint aside, and just set the `DEFAULT`? Let's try with the `date_first_login` new column 

```sql
ALTER TABLE users ADD COLUMN date_first_login DATE DEFAULT CURRENT_DATE;
```

Checking what's in the table, we see all the `date_first_login` filled

```
 id |     username      | name  | surname | age | points | date_first_login 
----+-------------------+-------+---------+-----+--------+------------------
  1 | jdoe              | Jon   | Doe     |  25 |      0 | 2024-02-27
  2 | lspencer          | Liz   | Spencer |  35 |      0 | 2024-02-27
  3 | hlondon           | Hanna | London  |  45 |      0 | 2024-02-27
  4 | test              | Hugo  |         |     |      0 | 2024-02-27
  5 | test1             | Hugo  |         |     |      0 | 2024-02-27
  7 | usrwithnullpoints | Hugo  |         |     |      0 | 2024-02-27
(6 rows)
```

If we try to insert a new row, without specifying the `date_first_login`, it's correctly assigned to today

```sql
INSERT INTO users (username) VALUES ('user_with_no_date_first_login');
```

The above is correctly inserted with a not null `date_first_login`

```
 id |           username            | name  | surname | age | points | date_first_login 
----+-------------------------------+-------+---------+-----+--------+------------------
  1 | jdoe                          | Jon   | Doe     |  25 |      0 | 2024-02-27
  2 | lspencer                      | Liz   | Spencer |  35 |      0 | 2024-02-27
  3 | hlondon                       | Hanna | London  |  45 |      0 | 2024-02-27
  4 | test                          | Hugo  |         |     |      0 | 2024-02-27
  5 | test1                         | Hugo  |         |     |      0 | 2024-02-27
  7 | usrwithnullpoints             | Hugo  |         |     |      0 | 2024-02-27
  9 | user_with_no_date_first_login | Hugo  |         |     |      0 | 2024-02-27
(7 rows)
```

So... is the `NOT NULL` constraint really needed? The reply is **YES** since I could insert `NULLs` by specifying it in the `INSERT` statement

```sql
INSERT INTO users (username, date_first_login) VALUES ('user_with_null_date_first_login', NULL);
```

Which will be stored correctly with a `NULL` value for `date_first_login`

```
 id |            username             | name  | surname | age | points | date_first_login 
----+---------------------------------+-------+---------+-----+--------+------------------
  1 | jdoe                            | Jon   | Doe     |  25 |      0 | 2024-02-27
  2 | lspencer                        | Liz   | Spencer |  35 |      0 | 2024-02-27
  3 | hlondon                         | Hanna | London  |  45 |      0 | 2024-02-27
  4 | test                            | Hugo  |         |     |      0 | 2024-02-27
  5 | test1                           | Hugo  |         |     |      0 | 2024-02-27
  7 | usrwithnullpoints               | Hugo  |         |     |      0 | 2024-02-27
  9 | user_with_no_date_first_login   | Hugo  |         |     |      0 | 2024-02-27
 10 | user_with_null_date_first_login | Hugo  |         |     |      0 | 
(8 rows)
```

**💡 Lesson 5 💡**: The `DEFAULT` is going to prevent `NULL`s from being inserted only if the `NULL` is **NOT** specified in the target column. To enforce the value we need an explicit `NOT NULL` constraint

## Querying NULLs

In the above section we tried to avoid storing `NULL`s in our systems, what if we need to deal with existing `NULL`s in our column? Here we can use a few SQL functions to detect them and change their value.

### Detecting NULLs with the `IS NULL` condition

What if we want to detect the `NULL` values? We can use the `IS NULL` condition, for example:

```sql
SELECT * from users where surname IS NULL;
```

Which will retrieve the list of `NULL`s surnames

```
 id |            username             | name | surname | age | points | date_first_login 
----+---------------------------------+------+---------+-----+--------+------------------
  4 | test                            | Hugo |         |     |      0 | 2024-02-27
  5 | test1                           | Hugo |         |     |      0 | 2024-02-27
  7 | usrwithnullpoints               | Hugo |         |     |      0 | 2024-02-27
  9 | user_with_no_date_first_login   | Hugo |         |     |      0 | 2024-02-27
 10 | user_with_null_date_first_login | Hugo |         |     |      0 | 
(5 rows)
```

The opposite list could be retrieved by applying the `IS NOT NULL` condition:

```sql
SELECT * from users where surname IS NOT NULL;
```

Resulting in

```
 id | username | name  | surname | age | points | date_first_login 
----+----------+-------+---------+-----+--------+------------------
  1 | jdoe     | Jon   | Doe     |  25 |      0 | 2024-02-27
  2 | lspencer | Liz   | Spencer |  35 |      0 | 2024-02-27
  3 | hlondon  | Hanna | London  |  45 |      0 | 2024-02-27
(3 rows)
```

What if I try to use the equal comparison (`=`)

```sql
SELECT * from users where surname = NULL;
```

The above retrieves **NO rows** since the `NULL` value means there is **NO value** and is **NOT** equal to any other value (`NULL`=`NULL` is **False**)

```
 id | username | name | surname | age | points | date_first_login 
----+----------+------+---------+-----+--------+------------------
(0 rows)
```

If we apply the different operator (`<>`) with `NULL` we also get back **NO rows**, since the `NULL` value means there is **NO value**, and therefore is not equal or different to other values.

```sql
SELECT * from users where surname <> NULL;
```

**💡 Lesson 6 💡**: To find `NULL`s we should always use the `IS NULL`; the `IS NOT NULL` to fetch the not null rows.

### Associating a default value to NULLs with COALESCE

In the above we saw how to find `NULL`s, what if we need to include them in a `SELECT` statement with other values? We can either add an `OR` condition like

```sql
SELECT * from users 
WHERE surname = 'London' 
OR surname IS NULL;
```

Or we can apply a transformation to change the `NULL`s into a value we can compare

```sql
SELECT * from users 
WHERE COALESCE(surname,'Verona') in ('London', 'Verona');
```

Both will have the same result

```
 id |            username             | name  | surname | age | points | date_first_login 
----+---------------------------------+-------+---------+-----+--------+------------------
  3 | hlondon                         | Hanna | London  |  45 |      0 | 2024-02-27
  4 | test                            | Hugo  |         |     |      0 | 2024-02-27
  5 | test1                           | Hugo  |         |     |      0 | 2024-02-27
  7 | usrwithnullpoints               | Hugo  |         |     |      0 | 2024-02-27
  9 | user_with_no_date_first_login   | Hugo  |         |     |      0 | 2024-02-27
 10 | user_with_null_date_first_login | Hugo  |         |     |      0 | 
(6 rows)
```

However, if we apply the second option, we must be sure that `Verona` (the label we associate to `NULL` values) is a value not present in the column or a value we want to include in our selection.

**💡 Lesson 7 💡**: When using `COALESCE` check out that the value you want to use as substitute of `NULL` is not already present in the table and outside of your business logic otherwise you might include in the filter more values than expected.

### Performing Math on NULL columns

A similar scenario is due when we need to perform a calculation over a `NULL`able column. Let's take the example of the `age` column: what if we want to calculate the average age?

Let's see the data:

```
 id |            username             | name  | surname | age | points | date_first_login 
----+---------------------------------+-------+---------+-----+--------+------------------
  1 | jdoe                            | Jon   | Doe     |  25 |      0 | 2024-02-27
  2 | lspencer                        | Liz   | Spencer |  35 |      0 | 2024-02-27
  3 | hlondon                         | Hanna | London  |  45 |      0 | 2024-02-27
  4 | test                            | Hugo  |         |     |      0 | 2024-02-27
  5 | test1                           | Hugo  |         |     |      0 | 2024-02-27
  7 | usrwithnullpoints               | Hugo  |         |     |      0 | 2024-02-27
  9 | user_with_no_date_first_login   | Hugo  |         |     |      0 | 2024-02-27
 10 | user_with_null_date_first_login | Hugo  |         |     |      0 | 
(8 rows)
```

We can calculate the average age with:

```sql
select avg(age) 
from users;
```

Resulting in `35` which is the average between the 3 **NOT NULL** ages! Similarly if we count the `date_first_login`s with:

```sql
select count(date_first_login) 
from users;
```

The result is `7`, one row for every user having a not null `date_first_login`

**💡 Lesson 8 💡**: When performing Mathematical operation with nullable columns, only **NOT NULL** values will be elaborated!

What if we try to calculate the average `points`? First let's update the table to give `jdoe` 25 points

```sql
UPDATE users set points=25 where username='jdoe';
```

Now let's calcuate the average points

```sql
select avg(points) from users;
```

The result is `3.125` which is the mathematical average between the `25` points of `jdoe` and the `0` of the rest due to us previously setting `0` as default value. Is this correct? It depends! If we wanted to calculate the average number of points of users actually playing some games, this could be a misleading calculation.

**💡 Lesson 9 💡**: When performing Mathematical operation with nullable columns, the default value of a column will impact the results.

### Joining on NULL columns

Building on what we explored before about the `NULL` not being equal or different to any other value, we need to pay closer attention when we deal with nullable columns in join condition. 


Let's create another table called `sessions` and insert some data. For a weird reason, the design of this table will force us to join with `users` on the `surname` which is a NULLABLE field.

```sql
CREATE TABLE sessions (id serial, surname text, session_time timestamp);
INSERT INTO sessions (surname, session_time) values
    ('Doe', CURRENT_TIMESTAMP - INTERVAL '1 day'),
    ('Doe', CURRENT_TIMESTAMP - INTERVAL '1 day'*2),
    ('Spencer', CURRENT_TIMESTAMP - INTERVAL '1 day'*2),
    ('Spencer', CURRENT_TIMESTAMP),
    (NULL, CURRENT_TIMESTAMP),
    (NULL, CURRENT_TIMESTAMP - INTERVAL '1 day'*2),
    (NULL, CURRENT_TIMESTAMP - INTERVAL '1 day'*2),
    (NULL, CURRENT_TIMESTAMP - INTERVAL '1 day'*1),
    (NULL, CURRENT_TIMESTAMP);
```

Now, if we want to count users having a session on each day we could think of simply writing the above query:

```sql
SELECT EXTRACT(DAY FROM session_time),
    count(distinct username) 
FROM sessions join users 
    on sessions.surname=users.surname
group by EXTRACT(DAY FROM session_time);
```

However the results shows only the 4 sessions below

```
 extract | count 
---------+-------
      25 |     2
      26 |     1
      27 |     1
(3 rows)
```

Why? It's again because of `NULL` values in the join condition. This problem of poor data quality on both ends of the join is stopping us from reporting any useful number. How could we fix it? Well, the fix should be done uphill by setting the `sessions` table to use the `username` instead of the `surname` and defining a proper `FOREIGN KEY` to the `users` table.

**💡 Lesson 10 💡**: Joining tables on nullable columns without caring about the NULL management will impact the results by reducing the number of rows included in the join.

Still, if we want a simplistic solution, we could do the apply the `COALESCE` to both sides of the join:

```sql
SELECT EXTRACT(DAY FROM session_time),
    count(distinct username) 
FROM sessions join users 
    on coalesce(sessions.surname, 'NoSurname')=coalesce(users.surname, 'NoSurname')
group by EXTRACT(DAY FROM session_time);
```

The result shows an increased count of sessions

```
 extract | count 
---------+-------
      25 |     7
      26 |     6
      27 |     6
(3 rows)
```

However this is still not the correct answer, since we are seeing `19` session being aggregated while only `9` sessions were inserted in the original `sessions` table. Why is that? It's because we now casted all the `NULL`s as `NoSurname` on both sides of the join. Since we had `5` sessions without `surname` and `5` users without `surname` we are creating a cartesian join of all the empty surnames with the empty sessions.

We had the following users without `surname`

```
test
test1
usrwithnullpoints
user_with_no_date_first_login
user_with_null_date_first_login
```

And the following sessions without `surname`

```
  6 | 2024-02-27 14:10:08.419021
  7 | 2024-02-25 14:10:08.419021
  8 | 2024-02-25 14:10:08.419021
  9 | 2024-02-26 14:10:08.419021
 10 | 2024-02-27 14:10:08.419021
```

Each of the users was associated with each sessions, creating a overflow of sessions.

A more correct solution, attribuiting all the `NULL` session to a same phantom user `NoUser` could be:

```sql
SELECT EXTRACT(DAY FROM session_time),
    count(distinct coalesce(username,'NoUser'))
FROM sessions left outer join users 
    on sessions.surname=users.surname
group by EXTRACT(DAY FROM session_time);
```

However this is an effort to fix data quality issues at query time, which is never a good idea.

**💡 Lesson 11 💡**: Fixing data quality issues related to nullable columns at query time is a risky approach. Solving them with proper table design and constraints allows you to keep quality data in the tables.


## Conclusion

Dealing with `NULL`s is something to care upfront, during table design, in order to avoid data quality issues downstream! 
This is it for the 11 lessons learnt using `NULL`s, do you have others to share? Feel free to reach me on X/Twitter at @ftisiot or on [Linkedin](https://www.linkedin.com/in/francescotisiot/)