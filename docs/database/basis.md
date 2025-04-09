I don't...
Title: Database Basics Summary
Category: Database
Tag:

- Database basics
  I don't...

@include: @small-advertisement.snippet.md-->

The knowledge base of the database, part of which must understand memory. Although this part is theoretical knowledge, it is very important, and it is the basis for learning from the MySQL database later. PS: This part of the section, because of its conceptual content, refers to the corresponding descriptions of Wikipedia and encyclopedia.

# What is a database, database management system, database system, database administrator?

- **Database**: Database (DataBase) is a collection of information or data managed by a database management system.
- **Database Management System**: Database Management System (DBMS) is a large software for the manipulation and management of databases, usually for the establishment, use, and maintenance of databases.
- **Database System**: Database System (Data Base System, known as DBS) usually consists of software, database, and data administrators (DBA).
- **Database Administrator**: Database Administrator, known as DBA, is responsible for the overall management and control of the database system.

# What are the elements, code, candidate code, main code, foreign code, main property, non-main property?

- **Metagroup**: Metagroup (tuple) is the basic concept in the relational database. The relationship is a table in which each line (i.e., each record in the database) is a metagroup and each column is an attribute. In a two-dimensional view, it's also called rows.
- **Code**: Code is the attribute that identifies the only entity, corresponding to the column in the table.
- **Candidate**: If the value of a given attribute or group of attributes in the relationship can only identify a group of elements that can no longer be identified, the group of attributes is referred to as a candidate. For example, in student entities, “school numbers” are the only ones that distinguish between student entities, assuming that the combination of “names” and “classes” is sufficient to distinguish between student entities, so {school numbers} and {names, classes} are candidates.
- **Mastername**: Mastername is also called Main key. The main code is selected from the candidate code. A single entity can only have one master code but can have more than one candidate.
- **Foreign name**: Also called Foreign key. If one attribute of a relationship is the main code of another relationship, the attribute is the foreign key.
- **Main Properties**: Properties that appear in the candidate code are referred to as Main Properties. For example, in relationships, workers (work numbers, ID numbers, names, sex, department), obviously, the work and ID numbers are the only signs of this relationship, so they're all candidates. The main attributes are the work and ID numbers. If the master code is a property group, the attribute in the attribute group is the main attribute.
- **Non-main property**: Properties not contained in any candidate code are referred to as non-main properties. For example, in relationships - students (school number, name, age, sex, class) the main code is “school number”, so other “name”, “age”, “sex”, and “class” can all be referred to as non-main properties.

What's an ER chart?

When we do a project, we have to try to draw ER maps to clean up the database design, and this is what interviewers often ask you about the project.

**ER** Full name is Entity Relationship Diagram (Entity Contact Chart), which provides a methodology for the expression of entity type, attributes, and linkages.

The ER diagram consists of the following three elements:

- **Entity**: Usually a real-world operational object; of course, some logical objects can be used. For example, for a school management system, students, teachers, curricula, classes, etc. are involved. In ER diagrams, entities use rectangle frames.
- **Properties**: That is, properties owned by an entity, which are used to describe the elements of the constituent entity and can be understood as fields for product design. In ER charts, properties are expressed in elliptical form.
- **Contact**: The relationship between the entity and the entity, expressed in the ER diagram in a diamond-shaped form, is not only business-related but also numerically a quantitative comparison between the entities. For example, a class with multiple students is an inter-entity link.

The figure below is an ER for student selection, where each student can choose a number of subjects and the same subject can be selected by a number of people, so that their relationship is many-to-many (M:N). In addition, there are two other types of relationships: one-to-one, one-to-many (1:N).

(https://oss.javaguide.cn/github/javaguide/csdn/c745c87f6eda9a439eeea52012c7f4a.png)

Do you understand the database paradigm?
