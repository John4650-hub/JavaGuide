---
title: One Thousand Lines of MySQL Learning Notes
category: Database
tag:
  - MySQL
---

> Original address: <https://shockerli.net/post/1000-line-mysql-note/>. JavaGuide has made a simple layout of this article and added a table of contents.

A very good summary, strongly recommended to save it for reference when needed.

### Basic Operations

```sql
/* Windows Service */
-- Start MySQL
			net start mysql
-- Create Windows Service
				sc create mysql binPath= mysqld_bin_path (Note: there is a space between the equal sign and the value)
/* Connect and Disconnect from Server */
-- Connect to MySQL
				mysql -h address -P port -u username -p password
-- Show which threads are running
				SHOW PROCESSLIST
-- Show system variable information
				SHOW VARIABLES
```

### Database Operations

```sql
/* Database Operations */
-- View current database
    SELECT DATABASE();
-- Show current time, username, database version
    SELECT now(), user(), version();
-- Create database
    CREATE DATABASE [IF NOT EXISTS] database_name database_options
    Database options:
        CHARACTER SET charset_name
        COLLATE collation_name
-- View existing databases
    SHOW DATABASES [LIKE 'PATTERN']
-- View current database information
    SHOW CREATE DATABASE database_name
-- Modify database options
    ALTER DATABASE database_name options
-- Delete database
    DROP DATABASE [IF EXISTS] database_name
        Also deletes the directory related to the database and its contents
```

### Table Operations

```sql
/* Table Operations */
-- Create table
    CREATE [TEMPORARY] TABLE [IF NOT EXISTS] [database_name.]table_name (table structure definition) [table options]
        Each field must have a data type
        No comma after the last field
        TEMPORARY temporary table, automatically disappears at the end of the session
        For field definitions:
            field_name data_type [NOT NULL | NULL] [DEFAULT default_value] [AUTO_INCREMENT] [UNIQUE [KEY] | [PRIMARY] KEY] [COMMENT 'string']
-- Table options
    -- Character set
        CHARSET = charset_name
        If the table is not set, the database character set is used
    -- Storage engine
        ENGINE = engine_name
        Different data structures used by the table when managing data, different structures lead to different processing methods and features
        Common engines: InnoDB, MyISAM, Memory/Heap, BDB, Merge, Example, CSV, MaxDB, Archive
        Different engines use different methods to save table structures and data
        MyISAM table file meanings: .frm table definition, .MYD table data, .MYI table index
        InnoDB table file meanings: .frm table definition, table space data, and log files
        SHOW ENGINES -- Show storage engine status information
        SHOW ENGINE engine_name {LOGS|STATUS} -- Show storage engine logs or status information
    -- Auto-increment starting number
    	AUTO_INCREMENT = row_number
    -- Data file directory
        DATA DIRECTORY = 'directory'
    -- Index file directory
        INDEX DIRECTORY = 'directory'
    -- Table comment
        COMMENT = 'string'
    -- Partition options
        PARTITION BY ... (see manual for details)
-- View all tables
    SHOW TABLES [LIKE 'pattern']
    SHOW TABLES FROM database_name
-- View table structure
    SHOW CREATE TABLE table_name (more detailed information)
    DESC table_name / DESCRIBE table_name / EXPLAIN table_name / SHOW COLUMNS FROM table_name [LIKE 'PATTERN']
    SHOW TABLE STATUS [FROM db_name] [LIKE 'pattern']
-- Modify table
    -- Modify table options
        ALTER TABLE table_name table options
        eg: ALTER TABLE table_name ENGINE=MYISAM;
    -- Rename table
        RENAME TABLE old_table_name TO new_table_name
        RENAME TABLE old_table_name TO database_name.table_name (can move the table to another database)
        -- RENAME can swap two table names
    -- Modify table field structure (13.1.2. ALTER TABLE syntax)
        ALTER TABLE table_name operation_name
        -- Operation names
            ADD [COLUMN] field definition       -- Add field
                AFTER field_name          -- Indicates adding after this field name
                FIRST               -- Indicates adding at the first position
            ADD PRIMARY KEY (field_name)   -- Create primary key
            ADD UNIQUE [index_name](field_name) -- Create unique index
            ADD INDEX [index_name](field_name) -- Create normal index
            DROP [COLUMN] field_name      -- Delete field
            MODIFY [COLUMN] field_name field attributes     -- Supports modifying field attributes, cannot modify field name (all original attributes must also be written)
            CHANGE [COLUMN] old_field_name new_field_name field attributes     