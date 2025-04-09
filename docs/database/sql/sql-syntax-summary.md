I don't...
Title: Summary of Basic Knowledge of SQL Grammar
Category: Database
Tag:

- Database Basics
- SQL
  I don't...

> This paper has been refined from the following two sources:
>
> - [SQL Grammar Speed Manual](https://juejin.cn/post/684490379057700231)
> - [MySQL Supercurricular Academy](https://www.begtut.com/mysql/mysql-tutorian.html)

# Basic Concepts

## Database Terminology

- `database` - a container (usually a file or group of documents) where organized data is kept.
- `Data Table (table)` - Structured list of certain types of data.
- `Schema` - Information on the layout and characteristics of databases and tables. The model defines how the data is stored in the table, including information on what data is stored, how the data is broken down, and how the information is named. Both the database and the table have a pattern.
- `column` - a field in a table. All tables consist of one or more columns.
- `row` - A record in a table.
- `Main key (primary key)` - a column (or set of columns) with a value capable of identifying each row in the single list.

## SQL Syntax

SQL (Structured Query Language), Standard SQL is administered by the ANSI Standards Committee and is known as ANSI SQL. Each DBMS has its own realization, such as PL/SQL, Transact-SQL, etc.

### SQL Syntax Structure

SQL syntax structure includes:

- **`Subparagraph`** - is a component of statements and queries. (In some cases, these are optional. I'm not sure.)
- **`expression`** - Can produce any metric values, or database tables by columns and rows.
- **`Phrasing`** - The SQL Trivalent Logic (3VL) (true/false/unknown) or Boolean Aspect Conditions for Needs Assessment, with limits on the effects of statements and queries, or changes in process.
- **`Query`** - Retrieving data on specific conditions. This is an important part of SQL.
- **`Statement`** - can have a lasting impact on outline and data and can control database services, process processes, connections, sessions, or diagnostics.

### SQL Syntax Points

- **SQL statements are not case-sensitive**, but database table names, listings, and values are differentiated, depending on specific DBMS and configurations. For example, `SELECT` is the same as `select`, `September`.
- **Multiple SQL statements must be separated by semicolons (`;`)**.
- When handling SQL statements, **all spaces are ignored**.

SQL statements can be written in one line or in multiple rows.

```sql
-- Line SQL Statement

UPDATE user SET username='robot', password='robot' WHERE username='root';

-- Multiline SQL statement
UPDATE user
SET username='robot', password='robot'
WHERE username='root';
```

SQL supports three comments:

```sql
# Comment 1
-- Comment 2
/* Note 3 */
```

## SQL Classification

### Data Definition Language (DDL)

Data Definition Language (DDL) is the SQL language that focuses on data structure definitions and database object definitions.

The main function of DDL is to define the database object.

The core DDL directives are `CREATE`, `ALTER`, `DROP`.

### Data Manipulation Language (DML)

Data Manipulation Language (DML) is a programming statement for database operations to access objects and data running in the database.

The main function of DML is **access to data**, so its grammar is based on **reading and writing database**.

The core DML directives are `INSERT`, `UPDATE`, `DELETE`, `SELECT`. These four commands are collectively referred to as CRUD (Create, Read, Update, Delete), i.e., add, delete, and recheck.

### Transaction Control Language (TCL)

Transaction Control Language (TCL) is used to manage services in the database. These are used to manage changes made by the DML statement. It also allows statements to be grouped into logical matters.

The core directive of TCL is `COMMIT`, `ROLLBACK`.

### Data Control Language (DCL)

Data Control Language (DCL) is a command that controls access to data and controls the control of selected user accounts over database objects such as data sheets, view tables, pre-storage programs, user-defined functions, etc.

The core directive of DCL is `GRANT`, `REVOKE`.

The DCL, which is based on **access rights for users**, is not complicated by its directive practice and can be used for DCL controls such as: `CONNECT`, `SELECT`, `INSERT`, `UPDATE`, `DELETE`, `EXECUTE`, `USAGE`, `REFERENCES`.

Depending on the DBMS and the different security entities, the control of the authority they support also varies.

\*\*Let's start with the DML use. The primary function of DML
