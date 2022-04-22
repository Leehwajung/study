# Introduction to SQL

- [Data Definition](#data-definition)
    - [Domain Types in SQL](#domain-types-in-sql)
    - [Create Table Construct](#create-table-construct)
    - [Drop and Alter Table Constructs](#drop-and-alter-table-constructs)
- [Basic Query Structure](#basic-query-structure)
    - [The `select` Clause](#the-select-clause)
    - [The `where` Clause](#the-where-clause)
    - [The `from` Clause](#the-from-clause)
- [Additional Basic Operations](#additional-basic-operations)
    - [Joins](#joins)
    - [Natural Join](#natural-join)
    - [The Rename Operation](#the-rename-operation)
    - [String Operations](#string-operations)
    - [Ordering the Display of Tuples](#ordering-the-display-of-tuples)
    - [`where` Clause Predicates](#where-clause-predicates)
- [Set Operations](#set-operations)
- [Null Values](#null-values)
    - [Null Values and Three Valued Logic](#null-values-and-three-valued-logic)
- [Aggregate Functions](#aggregate-functions)
    - [Group By](#group-by)
    - [`having` Clause](#having-clause)
    - [Null Values and Aggregates](#null-values-and-aggregates)
- [Nested Subqueries](#nested-subqueries)
    - [Set Comparison](#set-comparison)
    - [Derived Relations](#derived-relations)
    - [`with` Clause](#with-clause)
    - [Scalar Subquery](#scalar-subquery)
- [Modification of the Database](#modification-of-the-database)
    - [Deletion](#deletion)
    - [Insertion](#insertion)
    - [Updates](#updates)

## Data Definition

- DDL: Data Definition Language

### Domain Types in SQL

- `char(n)`: Fixed length character string, with user-specified length `n`.
    - `n` 만큼 DBMS에 할당 -> 공간 낭비가 생길 수 있음.
- `varchar(n)`: Variable length character strings, with user-specified maximum length `n`.
    - 최대 `n` 이내에서 DBMS에 할당하는 크기를 값에 맞춤 -> 가변 길이이기 때문에 검색이 어려지는 등 DBMS 성능 저하의 원인이 될 수 있음.
- `int`: Integer (a finite subset of the integers that is machine-dependent).
- `smallint`: Small integer (a machine-dependent subset of the integer domain type).
- `numeric(p,d)`: Fixed point number, with user-specified precision of `p` digits, with `n` digits to the right of decimal point.
    - 자릿수까지 지정 -> 회계 등 정밀함이 필요한 데서 쓰임.
- `real, double precision`: Floating point and double-precision floating point numbers, with machine-dependent precision.
- `float(n)`: Floating point number, with user-specified precision of at least `n` digits.
    - 소수점 정밀도를 `n`으로 지정.

### Create Table Construct

```sql
create table r (A1 D1, A2 D2, ..., An Dn,
        (integrity-constraint1),
        ...,
        (integrity-constraintk))
```
- `r` is the name of the relation
- each `Ai` is an attribute name in the schema of relation `r`
- `Di` is the data type of values in the domain of attribute `Ai`
- `primary key` declaration on an attribute automatically ensures **not null**
- e.g.,
    ```sql
    create table instructor(
            ID        char(5),
            name      varchar(20) not null,
            dept_name varchar(20),
            salary    numeric(8,2),
            primary key(ID),
            foreign key(dept_name) references department)

    insert into instructor values('10211', 'Smith', 'Biology', 66000)
    insert into instructor values('10211', null, 'Biology', 66000)
    ```
    ```sql
    create table student(
            ID        varchar(5) primary key,
            name      varchar(20) not null,
            dept_name varchar(20),
            tot_cred  numeric(3,0),
            foreign key(dept_name) references department)

    create table takes(
            ID        varchar(5) primary key,
            course_id varchar(8),
            sec_id    varchar(8),
            semester  varchar(6),
            year      numeric(4,0),
            grade     varchar(2),
            foreign key(ID) references student,
            foreign key(course_id, sec_id, semester, year) references section)
    ```

### Drop and Alter Table Constructs

- `drop` table
    - instances까지 삭제됨.
    - table을 drop하면 storage 공간 할당이 연속적이지 않게 됨 -> 데이터를 내려 받은 후 다시 schema를 만들어 업로드하는 것을 추천
    - 표준도 있지만 지원하지 않는 DBMS가 많음.
- `alter` table
    - schema를 잘못 작성했을 때 사용하므로 사용하지 말 것.
    - `alter table r add A D`
        - where `A` is the name of the attribute to be added to relation `r` and `D` is the domain of `A`.
        - All tuples in the relation are assigned `null` as the value for the new attribute.
    - `alter table r drop A`
        - where `A` is the name of an attribute of relation `r`.
        - Dropping of attributes not supported by many databases.

## Basic Query Structure

- DML: Data Manipulation Language
- A typical SQL query has the form:
    ```sql
    select A1, A2, ..., An
    from r1, r2, ..., rm
    where P
    ```
    - `Ai` represents an attribute.
    - `ri` represents a relation.
    - `P` is a predicate.
- The result of an SQL query is a relation.

### The `select` Clause

- The `select` clause list the attributes desired in the result of a query
    - corresponds to the projection operation of the relational algebra
- Example: find the names of all `instructor`s:
    ```sql
    select name
    from instructor
    ```
- NOTE: SQL names are case insensitive (i.e., you may use upper-or lower-case letters.)
    - e.g., Name ≡ NAME ≡ name
    - Some people use upper case wherever we use bold font.
- SQL allows duplicates in relations as well as in query results.
    - 동명이인 등의 문제 때문에 중복 허용 -> 관계형 모델의 '이론'과 다른 점.
    - To force the elimination of duplicates, insert the keyword `distinct` after `select`.
    - Find the names of all departments with `instructor`, and remove duplicates:
        ```sql
        select distinct dept_name
        from instructor
        ```
    - The keyword `all` specifies that duplicates not be removed.
        ```sql
        select all dept_name
        from instructor
        ```
- An asterisk in the `select` clause denotes **"all attributes"**
    ```sql
    select * from instructor
    ```
- The `select` clause can contain arithmetic expressions involving the operation, `+`, `–`, `*`, and `/`, and operating on constants or attributes of tuples.
    - The query:
        ```sql
        select ID, name, salary/12
        from instructor
        ```
        would return a relation that is the same as the `instructor` relation, except that the value of the attribute `salary` is divided by 12.

### The `where` Clause

- The `where` clause specifies conditions that the result must satisfy
    - Corresponds to the selection predicate of the relational algebra.
- To find all `instructor`s in `'Comp. Sci.'` dept with `salary > 80000`
    ```sql
    select name
    from instructor
    where dept_name='Comp. Sci.' and salary > 80000
    ```
- Comparison results can be combined using the logical connectives `and`, `or`, and `not`.
- Comparisons can be applied to results of arithmetic expressions.

### The `from` Clause

- The `from` clause lists the relations involved in the query
    - Corresponds to the [Cartesian product](https://en.wikipedia.org/wiki/Cartesian_product) operation of the relational algebra.
- Find the Cartesian product instructor `X` teaches:
    ```sql
    select *
    from instructor, teaches
    ```
    - generates every possible instructor – teaches pair, with all attributes from both relations.
- Cartesian product not very useful directly, but useful combined with `where` clause condition (selection operation in relational algebra).

## Additional Basic Operations

### Joins

- Join이 가능하다는 점은 Table을 여러 개로 쪼갤 수 있는 이유.
- For all `instructor`s who have taught some course, find their names and the course ID of the courses they taught.
    ```sql
    select name, course_id
    from instructor, teaches
    where instructor.ID = teaches.ID
    ```
- Find the course ID, semester, year and title of each course offered by the `'Comp. Sci.'` department:
    ```sql
    select section.course_id, semester, year, title
    from section, course
    where section.course_id = course.course_id and dept_name = 'Comp. Sci.'
    ```
    - `and`의 우선 순위가 더 높음에 유의.

### Natural Join

- Natural join matches tuples with the same values for all common attributes, and retains only one copy of each common column
    ```sql
    select *
    from instructor
    natural join teaches
    ```
    - 연관된 attributes가 아닌데 join할 수 있으므로 안 쓰는 것이 좋음.
- NOTE: **Danger** in natural join: beware of unrelated attributes with same name which get equated incorrectly
    - 실수할 수 있으므로 실사용은 하지 않는 편이 좋음.

### The Rename Operation

- 이름을 변경한다기보다 다른 이름을 사용하겠다는 의미.
- The SQL allows renaming relations and attributes using the `as` clause:
    ```sql
    old_name as new_name
    ```
    - e.g.,
        ```sql
        select ID, name, salary/12 as monthly_salary
        from instructor
        ```
- Find the names of all `instructor`s who have a higher `salary` than some `instructor` in `'Comp. Sci.'`:
    ```sql
    select distinct T.name
    from instructor as T, instructor as S
    where T.salary > S.salary and S.dept_name = 'Comp. Sci.'
    ```
- Keyword `as` is optional and may be omitted; `instructor as T` ≡ `instructor T`
- 쿼리를 따로 날리는 것보다 하나의 쿼리로 날리면 DBMS가 잘 판단하여 더 효율적으로 동작함.
    - 필요한 내용을 하나의 쿼리문에 다 담는 것이 좋음.

### String Operations

- SQL includes a string-matching operator for comparisons on character strings. The operator `like` uses patterns that are described using two special characters:
    - percent (`%`): The `%` character matches any substring.
    - underscore (`_`): The `_` character matches any character.
- Find the names of all `instructor`s whose name includes the substring 'an':
    ```sql
    select name
    from instructor
    where name like '%an%'
    ```
- DBMS는 원래 정보 검색용으로 설계된 것이 아니기 때문에, 개수가 늘어나면 퍼포먼스가 낮아짐 -> [IR](https://en.wikipedia.org/wiki/Information_retrieval) 엔진을 따로 사용.
    - IR Serch Index: 본문이 1KB면 Index는 10KB정도 됨 -> 필요에 따라 인덱스를 새로 만들어 둠, 업데이트는 느림.

### Ordering the Display of Tuples

- List in alphabetic order the names of all `instructor`s
    ```sql
    select distinct name
    from instructor
    order by name
    ```
- We may specify `desc` for descending order or `asc` for ascending order, for each attribute; ascending order is the default.
    - e.g.,
        ```sql
        order by name desc
        ```
- Can sort on multiple attributes
    - e.g.,
        ```sql
        order by dept_name, name
        ```

### `where` Clause Predicates

- SQL includes a `between` comparison operator
    - e.g., Find the names of all `instructor`s with salary between $90,000 and $100,000 (that is, >= $90,000 and <= $100,000)
        ```sql
        select name
        from instructor
        where salary between 90000 and 100000
        ```
- Tuple comparison
    ```sql
    select name, course_id
    from instructor, teaches
    where (instructor.ID, dept_name) = (teaches.ID, 'Biology');
    ```

## Set Operations

- relation은 곧 tuples의 집합으로, 근본적으로 집합이므로 집합 연산이 가능.
- Find courses that ran in Fall 2009 or in Spring 2010:
    ```sql
    (select course_id from section where sem = 'Fall' and year = 2009)
    union
    (select course_id from section where sem = 'Spring' and year = 2010)
    ```
- Find courses that ran in Fall 2009 and in Spring 2010:
    ```sql
    (select course_id from section where sem = 'Fall' and year = 2009)
    intersect
    (select course_id from section where sem = 'Spring' and year = 2010)
    ```
- Find courses that ran in Fall 2009 but not in Spring 2010:
    ```sql
    (select course_id from section where sem = 'Fall' and year = 2009)
    except
    (select course_id from section where sem = 'Spring' and year = 2010)
    ```
    - 의미적으로 schema가 일치해야 함.
- Set operations `union`, `intersect`, and `except`
    - **Each of the above operations automatically eliminates duplicates** -> 일반적인 table 연산과 다름에 유의.
- To retain all duplicates use `union all`, `intersect all` and `except all`.
    - Suppose a tuple occurs `m` times in `r` and `n` times in `s`, then, it occurs:
        - `m + n` times in `r union all s`
        - `min(m,n)` times in `r intersect all s`
        - `max(0, m –n)` times in `r except all s`

## Null Values

- It is possible for tuples to have a null value, denoted by `null`, for some of their attributes
- `null` signifies an unknown value or that a value does not exist.
- The result of any arithmetic expression involving `null` is `null`
    - e.g., `5 + null returns null`
- The predicate `is null` can be used to check for `null` values.
    - e.g., Find all `instructor`s whose salary is `null`.
        ```sql
        select name
        from instructor
        where salary is null
        ```

### Null Values and Three Valued Logic

- Any comparison with `null` returns `unknown`
    - e.g., `5 < null` or `null <> null` or `null = null`
- Three-valued logic using the truth value `unknown`:
    - OR:
        ```
        (unknown or true) = true,
        (unknown or false) = unknown,
        (unknown or unknown) = unknown
        ```
    - AND:
        ```
        (true and unknown) = unknown,
        (false and unknown) = false,
        (unknown and unknown) = unknown
        ```
    - NOT:
        ```
        (not unknown) = unknown
        ```
    - `P is unknown` evaluates to true if predicate `P` evaluates to `unknown`
- Result of `where` clause predicate is treated as `false` if it evaluates to `unknown`
    - `where` caluse는 반드시 T/F를 판정해야 하는데, `unknown`이면 `true`를 장담할 수 없으므로 `false`를 반환.

## Aggregate Functions

- These functions operate on the multiset of values of a column of a relation, and return a value
    - `avg`: average value
    - `min`: minimum value
    - `max`: maximum value
    - `sum`: sum of values
    - `count`: number of values
- Find the average salary of `instructor`s in the Computer Science department:
    ```sql
    select avg (salary)
    from instructor
    where dept_name = 'Comp. Sci.'
    ```
- Find the total number of `instructor`s who teach a course in the Spring 2010 semester:
    ```sql
    select count (distinct ID)
    from teaches
    where semester = 'Spring' and year = 2010
    ```
- Find the number of tuples in the course relation:
    ```sql
    select count (*)
    from course
    ```

### Group By

- Find the average salary of `instructor`s in each department:
    ```sql
    select dept_name, avg (salary)
    from instructor
    group by dept_name
    ```
- Attributes in `select` clause outside of aggregate functions must appear in `group by` list
    ```sql
    /* erroneous query */
    select dept_name, ID, avg (salary)
    from instructor
    group by dept_name
    ```
    - `group by` 여러 개를 하나로 묶어서 하나의 attribute로 취급하라는 keyword인데, `group by`를 적용하는 ID의 값이 다 다르므로 오류 발생.

### `having` Clause

- Find the names and average salaries of all departments whose average salary is greater than 42000:
    ```sql
    select dept_name, avg (salary)
    from instructor
    group by dept_name
    having avg (salary) > 42000
    ```
    - `having`은 `group by`에 적용되는 조건으로서, `where`과는 다른 의미.
- Note: predicates in the `having` clause are applied after the formation of groups whereas predicates in the `where` clause are applied before forming groups.

### Null Values and Aggregates

- Total all salaries:
    ```sql
    select sum (salary)
    from instructor
    ```
  - Above statement ignores `null` amounts
  - Result is `null` if there is no non-null amount
- All aggregate operations except `count(*)` ignore tuples with null values on the aggregated attributes
- What if collection has only `null` values?
  - count returns 0
  - all other aggregates return `null`

## Nested Subqueries

- SQL provides a mechanism for the nesting of subqueries.
- A subquery is a select-from-where expression that is nested within another query.
- A common use of subqueries is to perform tests for set membership, set comparisons, and set cardinality.
- Find courses offered in Fall 2009 and in Spring 2010:
    ```sql
    select distinct course_id
    from section
    where semester = 'Fall' and year = 2009 and course_id 
    in (select course_id
        from section
        where semester = 'Spring' and year = 2010)
    ```
    - 이 예제는 간단하게 `and`를 사용하여 처리 가능하기 때문에, 굳이 subquery로 복잡하게 작성할 필요 없음.
- Find courses offered in Fall 2009 but not in Spring 2010:
    ```sql
    select distinct course_id
    from section
    where semester = 'Fall' and year = 2009 and course_id
    not in (select course_id
        from section
        where semester = 'Spring' and year = 2010)
    ```
- Find the total number of (distinct) studentswhohave taken course sections taught by the instructor with ID 10101:
    ```sql
    select count (distinct ID)
    from takes
    where (course_id, sec_id, semester, year)
    in (select course_id, sec_id, semester, year
        from teaches
        where teaches.ID= 10101)
    ```
    - Note: Above query can be written in a much simpler manner. The formulation above is simply to illustrate SQL features.

### Set Comparison

- Find names of `instructor`s with salary greater than that of some (at least one) instructor in the Biology department:
    ```sql
    select distinct T.name
    from instructor as T, instructor as S
    where T.salary > S.salary and S.dept_name= 'Biology'
    ```
- Same query using > `some` clause
    ```sql
    select name
    from instructor
    where salary > some (select salary
        from instructor
        where dept_name = 'Biology')
    ```
- Find the names of all `instructor`s whose salary is greater than the salary of all `instructor`s in the Biology department:
    ```sql
    select name
    from instructor
    where salary > all (select salary
        from instructor
        where dept_name = 'Biology')
    ```

### Derived Relations

- SQL allows a subquery expression to be used in the `from` clause
- Find the average `instructor`s' salaries of those departments where the average salary is greater than $42,000:
    ```sql
    select dept_name, avg_salary
    from (select dept_name, avg (salary) as avg_salary
        from instructor
        group by dept_name)
    where avg_salary > 42000
    ```
- Note that we do not need to use the `having` clause
- Another way to write above query
    ```sql
    select dept_name, avg_salary
    from (select dept_name, avg (salary)
        from instructor
        group by dept_name) as dept_avg (dept_name, avg_salary)
    where avg_salary> 42000
    ```

### `with` Clause

- The `with` clause provides a way of defining a temporary view whose definition is available only to the query in which the `with` clause occurs.
- Find all departments with the maximum budget:
    ```sql
    with max_budget (value) as
        (select max (budget)
        from department)
    select budget
    from department, max_budget
    where department.budget = max_budget.value
    ```
- Find all departments where the total salary is greater than the average of the total salary at all departments:
    ```sql
    with dept_total (dept_name, value) as
            (select dept_name, sum (salary)
            from instructor
            group by dept_name),
        dept_total_avg (value) as
            (select avg (value)
            from dept_total)
    select dept_name
    from dept_total, dept_total_avg
    where dept_total.value >= dept_total_avg.value
    ```

### Scalar Subquery

- e.g.,
    ```sql
    select dept_name,
        (select count (*)
        from instructor
        where department.dept_name= instructor.dept_name) as num_instructors
    from department
    ```
    - 위 subquery는 하나의 값으로 떨어지는 것이어야 함.

## Modification of the Database

### Deletion

- Delete all `instructor`s:
    ```sql
    delete from instructor
    ```
- Delete all `instructor`s from the Finance department:
    ```sql
    delete from instructor
    where dept_name = 'Finance'
    ```
- Delete all tuples in the instructor relation for those `instructor`s associated with a department located in the Watson building:
    ```sql
    delete from instructor
    where dept_name in (select dept_name
        from department
        where building = 'Watson')
    ```
- Delete all `instructor`s whose salary is less than the average salary of `instructor`s:
    ```sql
    delete from instructor
        where salary < (select avg (salary) from instructor)
    ```
    - 위 subquery는 하나의 값으로 떨어지므로 scalar query.

### Insertion

- Add a new tuple to `course`:
    ```sql
    insert into course
        values ('CS-437', 'Database Systems', 'Comp. Sci.', 4)
    ```
    - or equivalently:
        ```sql
        insert into course (course_id, title, dept_name, credits)
            values ('CS-437', 'Database Systems', 'Comp. Sci.', 4)
        ```
- Add a new tuple to `student` with `tot_creds` set to `null`:
    ```sql
    insert into student
        values ('3003', 'Green', 'Finance', null)
    ```
- Add all `instructor`s to the `student` relation with `tot_credsset` to 0:
    ```sql
    insert into student
        select ID, name, dept_name, 0
        from instructor
    ```

### Updates

- Increase salaries of `instructor`s whose `salary` is over $100,000 by 3%, and all others receive a 5% raise
    - Write two `update` statements:
        ```sql
        update instructor
            set salary = salary * 1.03
            where salary > 100000
        update instructor
            set salary = salary * 1.05
            where salary <= 100000
        ```
    - The order is important
    - Can be done better using the `case` statement:
        ```sql
        update instructor
            set salary = case
                when salary <= 100000 then salary * 1.05
                else salary * 1.03
                end
        ```
- Recomputeand update `tot_creds` value for all `student`s
    ```sql
    update student S
        set tot_cred = (select sum (credits)
            from takes natural join course
            where S.ID = takes.ID and takes.grade <> 'F' and takes.grade is not null)
    ```
- Sets `tot_creds` to null for `student`s who have not taken any course
- Instead of `sum (credits)`, use:
    ```sql
    case
        when sum (credits) is not null then sum (credits)
        else 0
    end
    ```
