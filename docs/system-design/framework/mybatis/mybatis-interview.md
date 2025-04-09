I don't...
Title: MyBatis Common Interview Summary\
Category: Frame\
icon: "database"\
Tag:\
MyBatis.\
Head:

- Meta.
- Name: keywords\
  MyBatis.
- Meta.
- Name: description\
  Several common MyBatis common\
  I don't...

@include: @small-advertition.snippet.md-->

> This article was collected by JavaGuide from the Internet and is unknown.
>
> I'd like you to look at the quality of MyBatis, which is recommended at the end of the paper.

What's the difference?

Note: The interview officer interviewed my colleagues.

Response:

- `${}` is a variable placeholder in a Properties file that can be used within the label property value and SQL, is replaced by the original text and can replace any content, such as `${driver}`, which will be replaced by `com.mysql.jdbc.Driver`.

Example: Sort by parameter by field:

```sql
SELECT * FROM us ORDER BY ${orderCols}
```

`orderCols` may be `name`, `name desc`, `name, sex asc`, etc. to achieve a flexible ranking.

- `#{}` is the SQL parameter placeholder, MyBatis will replace `#{}` in SQL with `?`, using the Prepared Statement parameter setting method before SQL is executed, setting parameters for SQL in order `?` placeholder, such as `ps.setInt(0, parameterValue)`, `#{item.name}` for using reflection to obtain the name attribute value of its object from the parameter object, which corresponds to `param.getItem().getName()`.

## XML map file, what are the labels in addition to the common labels of select, insert, update, delete?

Note: This question was asked by the Kyoto interview officer when I was interviewed.

Response: There are a number of other labels `resultMap`, `parameterMap`, `sql`, `<include>`, `<selectKey>`, plus nine dynamic SQL labels, `trim|choose|set|foreach|if|otherwise|bind`, among which `sql` is a SQL segment label that introduces SQL by `include` labels, and `<selectKey>` is a strategic label that does not support the growth of the main key.

### Dao interface what works? The method in the Dao interface, with different parameters, can the method be remounted?

Note: I was also interviewed by the Kyoto interview officer.

Response: In best practice, usually an XML map file is matched by a Dao interface. The Dao interface is commonly referred to as the `Mapper` interface, the full limit of the interface, the namespace value of the map file, the method of the interface, the id value of the map file, and the parameters in the interface method, the parameters passed to SQL. The `Mapper` interface is non-achievable, and when it is called to the interface method, the full limit + method spelling string is a key value, and the only one that can be located is a `MappedStatement`, for example: `com.mybatis3.mappers.StudentDao.findStudentById`, where the only namespace can be found as `com.mybatis3.mappers.StudentDao`. `id = MappedStatement` below In MyBatis, each of the labels `<select>`, `<insert>`, `<update>`, and `<delete>` will be deciphered as a `MappedStatement` object.

~ The method in the Dao interface cannot be remounted because it is the preservation and search strategy of the full name + method.

The method in the Dao interface can be remounted, but the ID in the XML of MyBatis does not allow repetition.

MyBatis version 3.3.0 with the following proxies:

```java
/**
 * Mapper interface reloading method
 */
This post is part of our special coverage Global Development 2011.

List<Student> getAllStu();

List<Student> getAllStu(@Param("id") Integer id);
```

This can then be achieved by using MyBatis's dynamic SQL in `StuMapper.xml`.

```xml
<select id="selectById" resultType="Student">
    SELECT * FROM student
    <where>
        <if test="id != null">
            id = #{id}
        </if>
    </where>
</select>
```

The method of writing overload in the Dao interface was achieved by functioning properly and obtaining the corresponding results.

**The Dao interface in MyBatis can have multiple reloading methods, but only one map of multiple interfaces must be available, otherwise the startup will miss.**

Related issue: [Correct: Dao] interface is remountable, but the ID in XML of MyBatis does not allow repeat! (https://github.com/Snailclimb/JavaGuide/issues/1122).

The working principle of the Dao interface is that the JDK Dynamic Proxy, whose JDK Dynamic Proxy is used for the Dao interface to generate the proxy object, and the proxy subject will intercept the interface method and then execute the SQL as represented by the `MappedStatement` and
