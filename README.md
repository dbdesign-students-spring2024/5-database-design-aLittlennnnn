# Data Normalization and Entity-Relationship Diagramming

## The Original Data
The following table shows the first few rows of the original table.
| assignment_id | student_id | due_date | professor | assignment_topic                | classroom | grade | relevant_reading    | professor_email   |
| :------------ | :--------- | :------- | :-------- | :------------------------------ | :-------- | :---- | :------------------ | :---------------- |
| 1             | 1          | 23.02.21 | Melvin    | Data normalization              | WWH 101   | 80    | Deumlich Chapter 3  | l.melvin@foo.edu  |
| 2             | 7          | 18.11.21 | Logston   | Single table queries            | 60FA 314  | 25    | Dümmlers Chapter 11 | e.logston@foo.edu |
| 1             | 4          | 23.02.21 | Melvin    | Data normalization              | WWH 101   | 75    | Deumlich Chapter 3  | l.melvin@foo.edu  |
| 5             | 2          | 05.05.21 | Logston   | Python and pandas               | 60FA 314  | 92    | Dümmlers Chapter 14 | e.logston@foo.edu |
| 4             | 2          | 04.07.21 | Nevarez   | Spreadsheet aggregate functions | WWH 201   | 65    | Zehnder Page 87     | i.nevarez@foo.edu |
| ...           | ...        | ...      | ...       | ...                             | ...       | ...   | ...                 | ...               |

By looking at the table, we can find the following problems that makes it not satisfying the fourth normal form:
- The "professor_email" field is an information about "professor", not the fact about the current primary key, which is the assignment_id. Due to this redundancy, if one of the professors change his email address, multiple updates will be made.
- Similarly, the "classroom" field is information about the course session, not the assignment.
- As mentioned in the assumptions, the due-date, assignment and classroom are based on sessions, which is information not provided by the original dataset.
- The table contain a mixture of information about five different entities: course sessions, assignments, grades, students,and professors. And there are more than one independent multi-valued facts about each of the entities in this tale, therefore it fundamentally violates the standard of 4NF.

## Tables after Converting to 4NF

### Table about Professors
I set a saparate table to store all the professor information. Here `professor_name` is the primary key.

| professor_name | professor_email   |
| :-------- | :---------------- |
| Melvin    | l.melvin@foo.edu  |
| Logston   | e.logston@foo.edu |
| Nevarez   | i.nevarez@foo.edu |
| ...       | ...               |

### Table about Assignments
A saparate table is used to store assignment information. The attributes include `assignment_id`, `session_id`, `professor_name`, `due_date`, `relevant_reading` and `topic`. Here `assignment_id` is the primary key, `session_id` is the foreign key to the session tables and `professor_name` is the foreign key to the professors table.

| assignment_id | topic                | relevant_reading    | professor_name | session_id | due_date |
| :------------ | :------------------------------ | :------------------ | :-------- | :-------- | :-------- |
| 1             | Data normalization              | Deumlich Chapter 3  | Melvin    | 1 | 23.02.21|
| 2             | Single table queries            | Dümmlers Chapter 11 | Logston   | 2 | 18.11.21|
| 5             | Python and pandas               | Dümmlers Chapter 14 | Logston   | 2 | 05.05.21|
| 4             | Spreadsheet aggregate functions | Zehnder Page 87     | Nevarez   | 3 | 04.07.21|
| ...           | ...                             | ...                 | ...       | ... |... |

### Table about Students
Here `student_id` is the primary key for the table.

| student_id |
| :----------|
| 1          |
| 2          |
| 3          |
| ...        |

### Table about Grades
Here `record_id` is the primary key, `student_id` is the foreign key for the students table and `assignment_id` is the foreign key for the assignments table.

| record_id | student_id | assignment_id | grade |
|:---------:|:----------:|:-------------:|:-----:|
|    1     |     1      |      1        |  80   |
|    2     |     7      |      2        |  25   |
|    3     |     4      |      1        |  75   |
|    4     |     2      |      4        |  92   |
|    5     |     2      |      5        |  65   |
|   ...    |    ...     |     ...       |  ...  |

### Table about sessions
Here `session_id` is the primary key, and it has attributes `classroom` and `professor_name`, where `professor_name` is the foreign key for the professor table.

| session_id | professor_name | classroom |
| :--------: | :------- | :-------- |
|     1     |  Melvin   |  WWH 101  |
|     2     |  Logston  |  60FA 314 |
|     3     |  Nevarez  |  WWH 201  |
|   ...     |   ...     |   ...     |

## ER Diagram
![ER Diagram](./images/ERDiagram.drawio.svg)

## Changes I have made
- I separated information in the original table into five categories, which are information about professors, assignments, students, grades and sections respectively.
- I created 1 table about professors containing professor names as the primary key and professor emails.
- I created 1 table about students which stores student ids. Additional information about the student such as names and email addresses can also be updated to this table if provided.
- I created 1 table about assignments containing assignment_id, assignment_topic, relevant_reading, due date, session it belogns to and the professors who assigned them. The table can be related to the tables of professors and sessions with the foreign keys "professor_name" and "session_id".
- I created 1 tabel about grades. With each pair of student and assignment I use a record_id to store the grade information, and use keys "student_id" and "assignment_id" to connect the table to the tables storing information about student and assignment.
- I created 1 table about sessions which stores the session_id, the classroom it takes place and the professor who teaches the session. With "prefessor_name" it is related to the table storing professor information.

No information has been abandoned, and a new surrogate key record_id were created.

With all the changes, now the information is presented in 5 different tables in 4NF, focusing on information about professors, students, assignments, grades and sessions. Each table depends on their own primary key and can be related to one another through foreign keys.
