---
title: SQL Setup, Insert, Update, and Delete
type: lesson
duration: "2:20"
creator:
    name: Jay Nappy
    modified by: Jonathan Ahrens (Taka)
    city: NYC
competencies: Databases
---

# SQL Setup, Insert, Update and Delete

### Objectives
*After this lesson, students will be able to:*

- [ ] Interact with the PSQL command line
- [ ] Create and DELETE a database
- [ ] Create and DELETE a table
- [ ] Insert, retrieve, update, and delete a row or rows into a database table

### Preparation
*Before this lesson, students should already be able to:*

- [x] `brew install postgresql`
- [x] Describe the relationship between tables, rows, and columns

## What are Databases? - Intro (20 mins)

A database is a place where information gets stored in a hard drive - or distributed across multiple hard drives - on a computer somewhere. Much like we've been creating and storing data, here and there, a database represents a collection of individual pieces of data stored in a highly structured and searchable way; they are able to represent a model of reality.

Inside a database, we do basic actions like create, read, update, and destroy data – these operataions are comonly refered to by the acronym CRUD and are essential to modern web development.

In modern web development, there are different categories of databases primarily SQL and NoSQL. We're focusing on SQL becuase it's a more general and robust solution.

SQL stands for Structured Query Language, and it's a language used to manage and get information from what are considered "relational" databases.

Wikipedia on relational databses:
>This model organizes data into one or more tables (or "relations") of columns and rows, with a unique key identifying each row. Rows are also called records or tuples.[3] Columns are also called attributes. Generally, each table/relation represents one "entity type" (such as customer or product). The rows represent instances of that type of entity (such as "Lee" or "chair") and the columns representing values attributed to that instance (such as address or price). -[wikipedia](https://en.wikipedia.org/wiki/Relational_database)


![relational db](https://cloud.githubusercontent.com/assets/25366/8589355/2646c588-25ca-11e5-9f2d-3d3afe8b7817.png)

## Ok, so we know about Databases, but what is SQL? Intro (10 mins)

Let's review: at it's simplest, a relational database is a mechanism to store and retrieve data in a tabular form.  Spreadsheets are a good analogy!  But how do we interact with our database: inserting data, updating data, retrieving data, and deleting data? That's where SQL comes in!

#### What is SQL?

SQL stands for Structured Query Language, and it is a language universally used and adapted to interact with relational databases.  When you use a SQL client and connect to a relational database that contains tables with data, the scope of what you can do with SQL commands includes:

- Inserting data
- Querying or retrieving data
- Updating or deleting data
- Creating new tables and entire databases
- Control permissions of who can have access to our data

Note that all these actions depend on what the database administrator sets for user permissions: a lot of times, as an analyst, for example, you'll only have access to retrieving company data; but as a developer, you could have access to all these commands and be in charge of setting the database permissions for your web or mobile application.

#### Why is SQL important?

Well, a database is just a repository to store the data and you need to use systems that dictate how the data will be stored and as a client to interact with the data.  We call these systems "Database Management Systems", they come in _many_ forms:

- MySQL
- SQLite
- PostgreSQL (what we'll be using!)

...and all of these management systems use SQL (or some adaptation of it) as a language to manage data in the system.


## Connect, Create a Database - Codealong (20 mins)

Let's make a database!  First, make sure you have PostgreSQL running.  Once you do, open your terminal and type:

```bash
$ psql
```

You should see something like:

```bash
your_user_name=#
```

To exit the psql CLI you can:
```bash
your_user_name=# \q
```
> Or you can <kbd>ctrl</kbd>+<kbd>d</kbd> out of it.  

Great! You've entered the PostgreSQL command line: now, you can execute PSQL commands, or PostgreSQL's version of SQL.

Let's use these commands, but before we can, we must create a database.  Let's call it wdi:

```psql
your_user_name=# CREATE DATABASE wdi;
CREATE DATABASE
```

The semicolon is important! Be sure to always end your SQL queries and commands with semicolons.

To delete the Database:
```bash
your_user_name=# DROP DATABASE wdi;
```

Let's list the Databases we have in psql
```psql
your_user_name=# \l
                                 List of databases
      Name      | Owner | Encoding |   Collate   |    Ctype    | Access privileges
----------------+-------+----------+-------------+-------------+-------------------
 debug_politics | taka  | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 hogs           | taka  | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 hogwarts_crud  | taka  | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 iparkmyself    | taka  | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
 moviehaus_db   | taka  | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
```

Now let's _use_ that database we just created:

```psql
your_user_name=# \c wdi
You are now connected to database "wdi" as user "your_user_name".
wdi=#
```

## Create a table - Demo (10 mins)

Now that we have a database, let's create a table (think of this like, "hey now that we have a workbook/worksheet, let's block off these cells with a border and labels to show it's a unique set of values"):

#### SQL style guide (see http://www.sqlstyle.guide)
1. Fields should *always* be lowercase
2. SQL _keywords_ SHOULD ALWAYS BE IN CAPS 
2. Never name a field `id`; always correlate it to the table name (e.g. `student_id`).
3. Always check your company's style guide, or follow the convention; never create your own style.

```sql
CREATE TABLE instructors (
  instructor_id SERIAL PRIMARY KEY NOT NULL,
  name TEXT NOT NULL,
  experience INT NOT NULL,
  website VARCHAR(50)
);
```

When we paste this into psql:

```psql
wdi=# CREATE TABLE instructors (
wdi(#  instructor_id  SERIAL PRIMARY KEY   NOT NULL,
wdi(#  name           TEXT          NOT NULL,
wdi(#  experience     INT           NOT NULL,
wdi(#  website        CHAR(50)
wdi(#  );
CREATE TABLE
```

Notice the different parts of these commands:

```psql
wdi=# CREATE TABLE instructors (
```
This starts our table creation, it tells PostgreSQL to create a table named "instructors"..

```psql
wdi(#  instructor_id        SERIAL   PRIMARY KEY   NOT NULL,
wdi(#  name      TEXT                NOT NULL,
```

...then, each line after denotes a new column we're going to create for this table, what the column will be called, the data type, whether it's a primary key, and whether the database - when data is added - can allow data without missing values.  In this case, we're not allowing `name`, `instructor_id` to be blank; but we're ok with `website` being blank.

> Read up on PSQL datatypes [here](https://www.postgresql.org/docs/10/static/datatype.html)  

## Create a student table and insert data - Codealong (10 mins)

Now that we're keeping track of our instructors, let's create a table for students that collects information about:

- an id that cannot be left blank
- the students name that cannot be left blank
- their age
- and their address that cannot be left blank.

Remembering the commands we just went over, students, try to guide the instructors through this!  

Here's what that query should have looked like:

```sql
CREATE TABLE students (
  student_id SERIAL PRIMARY KEY NOT NULL,
        name TEXT NOT NULL,
         age INT NOT NULL,
     address VARCHAR(50)
);
```

In psql that will look like:

```psql
wdi=# CREATE TABLE students (
wdi(#  student_id  SERIAL   PRIMARY KEY   NOT NULL,
wdi(#  name        TEXT                NOT NULL,
wdi(#  age         INT                 NOT NULL,
wdi(#  address     VARCHAR(50)
wdi(#  );
CREATE TABLE
```
Great job! Now let's finally _insert_ some data into that table - remember what cannot be left blank!

We'll insert five students, Jack, Jill, John, Jackie, and Slagathorn. The syntax is as follows:

```psql
INSERT INTO table_name VALUES (value1, value2, value3,...);
```

Let's do it for Jack, together:

```sql
INSERT INTO students VALUES (DEFAULT, 'Jack Sparrow', 28, '50 Main St, New York, NY');
```
In psql that will look like:

```psql
wdi=# INSERT INTO students VALUES (DEFAULT, 'Jack Sparrow', 28, '50 Main St, New York, NY');
INSERT 0 1
```

## Insert Data - Independent Practice (15 mins)

Now, you try it for the other students, and pay attention to the order of Jack's parameters and the single quotes - they both matter.

- Jill's full name is Jilly Cakes; she's 30 years old, and lives at 123 Webdev Dr. Boston, MA
- Johns's full name is Johnny Bananas; he's 25 years old, and lives at 555 Five St, Fivetowns, NY
- Jackie's full name is Jackie Lackie; she's 101 years old, and lives at 2 OldForThis Ct, Fivetowns, NY
- Slagathorn's full name is Slaggy McRaggy; he's 28 and prefers not to list his address

You should come up with:

```sql
INSERT INTO students VALUES (DEFAULT, 'Jilly Cakes', 30, '123 Webdev Dr. Boston, MA');
INSERT INTO students VALUES (DEFAULT, 'Johnny Bananas', 25, '555 Five St, Fivetowns, NY');
INSERT INTO students VALUES (DEFAULT, 'Jackie Lackie', 101, '2 OldForThis Ct, Fivetowns, NY');
INSERT INTO students VALUES (DEFAULT, 'Slaggy McRaggy', 28);
```

In psql this should look like:

```psql
wdi=# INSERT INTO students VALUES (DEFAULT, 'Jilly Cakes', 30, '123 Webdev Dr. Boston, MA');
INSERT 0 1
wdi=# INSERT INTO students VALUES (DEFAULT, 'Johnny Bananas', 25, '555 Five St, Fivetowns, NY');
INSERT 0 1
wdi=# INSERT INTO students VALUES (DEFAULT, 'Jackie Lackie', 101, '2 OldForThis Ct, Fivetowns, NY');
INSERT 0 1
wdi=# INSERT INTO students VALUES (DEFAULT, 'Slaggy McRaggy', 28);
INSERT 0 1
```

## BREAK!

## What's in our database? Code Along -  (15 mins)

So now that we have this data saved, we're going to need to access it at some point, right?  We're going to want to _select_ particular data points in our dataset provided certain conditions.  The PostgreSQL SELECT statement is used to fetch the data from a database table which returns data in the form of result table. These result tables are called result-sets. The syntax is just what you would have guessed:

```psql
SELECT column1, column2, column3 FROM table_name;
```
We can pass in what columns we want to look - like above - at or even get all our table records:

```psql
SELECT * FROM table_name;
```

For us, we can get all the records back:

```psql
wdi=# SELECT * FROM students;
 id |      name      | age |                      address
----+----------------+-----+----------------------------------------------------
  1 | Jack Sparrow   |  28 | 50 Main St, New York, NY
  2 | Jilly Cakes    |  30 | 123 Webdev Dr. Boston, MA
  3 | Johnny Bananas |  25 | 555 Five St, Fivetowns, NY
  4 | Jackie Lackie  | 101 | 2 OldForThis Ct, Fivetowns, NY
  5 | Slaggy McRaggy |  28 |
(5 rows)
```

We can get just the name and ages of our students:

```psql
wdi=# SELECT name, age FROM students;
      name      | age
----------------+-----
 Jack Sparrow   |  28
 Jilly Cakes    |  30
 Johnny Bananas |  25
 Jackie Lackie  | 101
 Slaggy McRaggy |  28
(5 rows)
```

#### Getting more specific

Just like JavaScript, all of our comparison and boolean operators can do work for us to select more specific data.

- I want the names of all the students who aren't dinosaurs - done:

```psql
wdi=# SELECT name FROM students WHERE age < 100;
      name
----------------
 Jack Sparrow
 Jilly Cakes
 Johnny Bananas
 Slaggy McRaggy
(4 rows)
```

- How about the names of students ordered by age? Done:

```psql
wdi=# SELECT name, age FROM students ORDER BY age;
      name      | age
----------------+-----
 Johnny Bananas |  25
 Jack Sparrow   |  28
 Slaggy McRaggy |  28
 Jilly Cakes    |  30
 Jackie Lackie  | 101
(5 rows)
```

- How about reversed? Ok:

```psql
wdi=# SELECT name, age FROM students ORDER BY age DESC;
      name      | age
----------------+-----
 Jackie Lackie  | 101
 Jilly Cakes    |  30
 Jack Sparrow   |  28
 Slaggy McRaggy |  28
 Johnny Bananas |  25
(5 rows)
```

- How about those who live in Fivetowns? We can find strings within strings too!

```psql
wdi=# SELECT * FROM students WHERE address LIKE '%Fivetowns%';
 id |      name      | age |                      address
----+----------------+-----+----------------------------------------------------
  3 | Johnny Bananas |  25 | 555 Five St, Fivetowns, NY
  4 | Jackie Lackie  | 101 | 2 OldForThis Ct, Fivetowns, NY
(2 rows)
```



## Updates to our database - Codealong (5 mins)

Ok, there are some mistakes we've made to our database, but that's cool, cause we can totally update it or delete information we don't like. Let's start by adding one more student:

```psql
wdi=# INSERT INTO students VALUES (DEFAULT, 'Miss Take', 500, 'asdfasdfasdf');
INSERT 0 1
```

But oh no, we messed them up - Miss Take doesn't live at asdfasdfasdf, she lives at 100 Main St., New York, NY.  Let's fix it:  

```psql
wdi=# UPDATE students SET address = '100 Main St., New York, NY' where address = 'asdfasdfasdf';
UPDATE 1

wdi=# SELECT * FROM students;
 id |      name      | age |                      address
----+----------------+-----+----------------------------------------------------
  1 | Jack Sparrow   |  28 | 50 Main St, New York, NY
  2 | Jilly Cakes    |  30 | 123 Webdev Dr. Boston, MA
  3 | Johnny Bananas |  25 | 555 Five St, Fivetowns, NY
  4 | Jackie Lackie  | 101 | 2 OldForThis Ct, Fivetowns, NY
  5 | Slaggy McRaggy |  28 |
  6 | Miss Take      | 500 | 100 Main St., New York, NY
(6 rows)
```

But wait, actually, she just cancelled - no big!

```psql
wdi=# DELETE FROM students where name = 'Miss Take';
DELETE 1

wdi=# SELECT * FROM students;
 id |      name      | age |                      address
----+----------------+-----+----------------------------------------------------
  1 | Jack Sparrow   |  28 | 50 Main St, New York, NY
  2 | Jilly Cakes    |  30 | 123 Webdev Dr. Boston, MA
  3 | Johnny Bananas |  25 | 555 Five St, Fivetowns, NY
  4 | Jackie Lackie  | 101 | 2 OldForThis Ct, Fivetowns, NY
  5 | Slaggy McRaggy |  28 |
(5 rows)

```

## Independent Practice - 15 mins

Using the same database from above, get through as many of these SQL challenges as you can.

- Insert five more students:
  - Nancy Gong is 40 and lives at 200 Horton Ave., Lynbrook, NY
  - Laura Rossi is 70 and listed her address as "Unlisted"
  - David Daniele is 28 and lives at 300 Dannington Ln., Washington, DC.
  - Greg Fitzgerald is 25 and did not list an address
  - Randi Fitz is 28 and lives in Oceanside, NY

- Randi wants her address to be corrected to 25 Broadway, New York, NY
- Get a list of student names and addresses who are older than 30 and order them by their address alphabetically
- Get a list of students whose first name begins with the letter "J"
- Get a list of student names who live in NY or MA

## Conclusion - 5 minutes

We will learn later on to relate our DB usage into backend services. 
SQL gives us a fast and reliable way to access data in a structured and logical way.

---

# Resources

## Further Reading
1. [www.sqlstyle.guide](http://www.sqlstyle.guide)
2. [PostgreSQL tutorial](http://www.tutorialspoint.com/postgresql/)
3. [PSQL datatypes](https://www.postgresql.org/docs/10/static/datatype.html)  

## Common Postgresql Commands

Here are a list of some common Postgresql commands that you might need:

- `\c` - connect to database
- `\l` - list all DBs
- `\d` - list details
- `\d+` - details of this table
- `\q` - exit psql
- `\h` - help


