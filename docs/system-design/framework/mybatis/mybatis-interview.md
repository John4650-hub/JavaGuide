---
title: Summary of Common MyBatis Interview Questions
category: Framework
icon: database
tag:
  - MyBatis
head:
  -   - meta
      - name: keywords
        content: MyBatis
  -   - meta
      - name: description
        content: A few common MyBatis interview questions
---

<!-- @include: @small-advertisement.snippet.md -->

> This article is collected from the internet by JavaGuide, original source unknown.
>
> Instead of these tedious interview questions, I recommend you check out the high-quality articles on MyBatis at the end of this article.

### What is the difference between #{} and ${}?

Note: This question was asked by the interviewer to my colleague.

Answer:

- `${}` is a variable placeholder in Properties files, which can be used in tag attribute values and within SQL. It performs a literal text replacement and can replace any content. For example, ${driver} will be replaced literally with `com.mysql.jdbc.Driver`.

An example: sorting by any field based on parameters:

```sql
select * from users order by ${orderCols}
```

`orderCols` can be `name`, `name desc`, `name,sex asc`, etc., allowing for flexible sorting.

- `#{}` is a parameter placeholder in SQL. MyBatis will replace `#{}` in SQL with a `?` mark. Before executing the SQL, it uses the parameter setting method of PreparedStatement to set parameter values for the `?` placeholders in order, such as ps.setInt(0, parameterValue). The value of `#{item.name}` is obtained by using reflection to get the name property value of the item object from the parameter object, equivalent to `param.getItem().getName()`.

### Besides the common select, insert, update, and delete tags, what other tags are there in the XML mapping file?

Note: This question was asked by the JD interviewer during my interview.

Answer: There are many other tags, such as `<resultMap>`, `<parameterMap>`, `<sql>`, `<include>`, `<selectKey>`, along with 9 dynamic SQL tags: `trim|where|set|foreach|if|choose|when|otherwise|bind`, etc. The `<sql>` tag is for SQL fragments, which can be included using the `<include>` tag, and `<selectKey>` is a tag for primary key generation strategies that do not support auto-increment.

### What is the working principle of the Dao interface? Can methods in the Dao interface be overloaded with different parameters?

Note: This question was also asked by the JD interviewer during my interview.

Answer: In best practices, typically, an XML mapping file will have a corresponding Dao interface. The Dao interface is commonly referred to as the `Mapper` interface. The fully qualified name of the interface is the value of the namespace in the mapping file, and the method names in the interface correspond to the `id` values of `MappedStatement` in the mapping file. The parameters in the interface methods are the parameters passed to SQL. The `Mapper` interface does not have an implementation class. When calling an interface method, the fully qualified name of the interface plus the method name is concatenated as a key to uniquely locate a `MappedStatement`. For example, `com.mybatis3.mappers.StudentDao.findStudentById` can uniquely find the `MappedStatement` with namespace `com.mybatis3.mappers.StudentDao` and `id = findStudentById`. In MyBatis, each `<select>`, `<insert>`, `<update>`, and `<delete>` tag is parsed into a `MappedStatement` object.

~~Methods in the Dao interface cannot be overloaded because of the strategy of saving and finding based on the fully qualified name and method name.~~

Methods in the Dao interface can be overloaded, but the IDs in MyBatis XML cannot be duplicated.

In MyBatis version 3.3.0, it has been tested as follows:

```java
/**
 * Method overloading in Mapper interface
 */
public interface StuMapper {

 List<Student> getAllStu();

 List<Student> getAllStu(@Param("id") Integer id);
}
```

Then, in `StuMapper.xml`, dynamic SQL can be utilized to achieve this.

```xml
<select id="getAllStu" resultType="com.pojo.Student">
  select * from student
  <where>
    <if test="id != null">
      id = #{id}
    </if>
  </where>
</select>
```

It runs normally and returns the corresponding results, thus achieving method overloading in the Dao interface.

**MyBatis's Dao interface can have multiple overloaded methods, but the mappings for multiple interfaces must be unique; otherwise, an error will occur on startup.**

Related issue: \[Correction: Methods in the Dao interface can be overloaded, but the IDs in MyBatis XML cannot be duplicated!\](https://github.com/Snailclimb/JavaGuide/issues/1122
