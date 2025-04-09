I don't...
type: 1,000 lines MySQL study notes
Category: Database
Tag:
- MySQL
I don't...

> Original address: <https://shockerli.net/post/1000-line-mysql-note/>. JavaGuide provides a short version of this paper with a new catalogue.

It is a very good summary, and it is strongly recommended that it be preserved and that it be seen when needed.

# Basic operations #

```sql
* Windows services */
- Start MySQL
No, no, no, no.
-- Create Windows Service
sc create mysql binPath = mysqld_bin_Path
* Connect and disconnect server */
-- Connect MySQL
mysql -h Address -P Port -u Username -p Password
-- Show which threads are running
SHOW PROCESSLIST
-- Display system variable information
SHOW VARIABLES
```

# Database operation

```sql
* Database operation */
-- View the current database
SELECT DATABASE();
-- Show current time, user name, database version
SELECT now(), user(), version();
-- Create Library
CREATE DATABASE [IF NOT EXISTS] Database_Name
Database options:
CHARACTER SET charset_name
COLLATE COLLATION_name
-- View existing libraries
SHOW DATABASES
- View current library information
SHOW CREATE DATABASE database_name
- Modify Library Options Information
ALTER DATABASE library_name
-- Delete Library
DROP DATABASE [IF EXISTS] database_name
Also delete the directories related to the database and their contents
```

# The table operation #

```sql
* The operation of the table */
-- Create Table
CREATE [TEMPORARY] TABLE [IF NOT EXISTS] [Customs.] Table_name (structure definition of tables) [table options]
Each field must have a data type
No commas after the last field
TEMPORARY temporary table, automatically disappears at the end of the session
Definition of fields:
Field_name Data_type [NOT NULL | NULL] [DEFAULT default_value] [AUTO_INCREMENT] [UNIQUE [KEY] | [PRIMARY] KEY] [COMMENT 'string']
- Table options
-- charset
Charset = charset_name
Use database charset if the table is not set
- Storage engine
ENGINE = engine_name
Tables use different data structures to manage data, and different structures lead to different treatments, features, etc.
Common engines: InnoDB, MyISAM, Memory/Heap, BDB, Merge, Example, CSV, MaxDB, Archive
Different engines use different ways to save table structure and data
MyISAM table document meaning: .frm table definition, .MYD table data, .MYI table index
InnoDB Table Document Meaning: .frm Table Definition, Table Spatial Data and Log Files
SHOW ENGINES - Show status information on storage engine
SHOW ENGINE Engine_Name {LOGS | STATUS} - Displays log or status information on the storage engine
-- Threshold for increase
AUTO_INCREMENT = Line_Number
-- Directory of data files
DATA DIRECTORY = 'Directory'
-- Index File Catalogue
INDEX DIRECTORY = 'Directory'
-- Notes
COMMENT = 'string'
- Partition Options
PARTITION BY...
- View all tables
SHOW TABLES
SHOW Tables FROM library_name
-- View Table Structure
SHOW CREATE TABLE Table_name (further details)
DESC Table_Name / DESCRIBE Table_Name / EXPLAIN Table_Name / SHOW COLUMNS FROM Table_Name [LIKE 'PATTERN']
SHOW TABLE STATUS [FROM db_name] [LIKE 'pattern']
-- Modify table
-- Options to modify the table itself
ALTER TABLE Table_Name Options
e.g., ALTER TABLE Table_name ENGINE=MYISAM;
- Rename table
RENAME TABLE Original_Table_Name TO New_Table_Name
RENAME TABLE Original_Table_name TO library_name.Table_name (can move table to another database)
- RENAME can exchange two table names.
- Field modifications to modify the table (13.1.2. ALTER TABLE)
ALTER TABLE Table_Name MODIFY field_name
-- Modifying field name
ADD [COLUMN] field_definition -- add field
AFTER field_name -- indicates an addition after the field name
FIRST -- indicates increase in first
ADD PRIMARY KEY (field_name) - Create Primary Key
ADD UNIQUE [index_name](field_name) - Create unique index
ADD INDEX [index_name](field_name) - Create Normal Index
DROP [COLUMN] field_name -- delete field
MODIFY [COLUMN] field_name Field_Properties - Support for changes to field properties, cannot change field names (all original attributes also need to be written)
CHANGE [COLUMN] Original_field_name New_field_name Field_Properties - Changes to field names are supported
DROP PRIMARY KEY - Delete Primary Key (to remove AUTO_INCREMENT properties before removing Primary Key)
DROP INDEX index_name - Delete Index
DROP FOREIGN KEY foreign_key -- delete foreign key
- Delete table
DROP TABLE [IF EXISTS] Table_name...
-- Empty table data
TRUNCATE [TABLE] Table_name
- Copy table structure
CREATE TABLE Table_name LIKE to_copy