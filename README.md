# Online Quiz System

## Project Overview
The **Online Quiz System** is designed to streamline online examination processes for universities, making assessments efficient, scalable, and user-friendly. Built using Oracle PL/SQL, this system automates quiz creation, administration, and grading, while providing performance analytics for teachers and students. The project is developed by the **Cardinals Group** to address challenges in online assessment management.The Online Quiz System is a database-driven application built with Oracle PL/SQL that allows teachers to create quizzes, students to take quizzes, and the system to grade and record scores automatically. It ensures secure access, prevents unauthorized data changes during restricted days, and supports performance tracking and reporting through MIS features like audit logs and result queries.

### Cardinals Group Members
1. Bakwiye Massa Patience       27388

---

## Project Phases

### Phase 2: 💼 Business Process Modeling
This phase focuses on defining and visualizing the workflow of the Online Quiz System.

- **Scope**: Centralize quiz management tasks, including quiz creation, student registration, and performance tracking.
- **Objectives**:
  - Automate quiz generation to save time for instructors.
  - Ensure secure and timed exams to prevent cheating.
  - Provide real-time performance analytics for teachers and students.
- **Entities**:
  - **Student**: Registers, takes quizzes, and views scores.
  - **Teacher**: Creates and manages quizzes, tracks student performance.
  - **Quiz**: Stores questions, time limits, and grading rules.
  - **Attempt**: Organizes teachers and students.
- **Significance in MIS**: The system supports decision-making by providing teachers with insights into student performance, enhancing efficiency in quiz administration.


---

<br>
<br>
<br>

![Image](https://github.com/user-attachments/assets/e9584636-dd7b-4d50-a8b7-437b445bc1cb)

<br>
<br>

### Phase 3: ⚙️ Logical Model Design
This phase provides a structured database schema to support the Online Quiz System.

- **Entities**:
  - , Teacher, Student, Quiz, Question, Submission, Grade, and Quiz Attempt.
- **Relationships**:
  - Defined using primary and foreign keys to connect students, teachers, quizzes, and submissions, ensuring accurate tracking of quiz attempts and grades.
- **Goals**:
  - Create an organized database structure to support secure and accessible data.
  - Enable efficient tracking of student progress and teacher workload.

---   ![Image](https://github.com/user-attachments/assets/83d27ccb-13b8-4f3c-8085-ad397a381fd6)

### Phase 4: 📡 Creating Database and Oracle Enterprise Manager

#### Creation of a Pluggable Database
A pluggable database named **TUE_CARDINALS_ONLINEQUIZSYSTEM** was created to store the system's data.

```sql
-- Step 1: Create the Pluggable Database (PDB)
CREATE PLUGGABLE DATABASE wed_27388_Massa_OnlineQuizSystem
ADMIN USER Patience IDENTIFIED BY Patience
ROLES = (DBA)
FILE_NAME_CONVERT = ('C:\app\MutayombaAimable\product\21c\oradata\XE\',
                         'C:\app\MutayombaAimable\product\21c\oradata\XE\wed_27388_Massa_OnlineQuizSystem_db\');



-- Step 2: Open the Pluggable Database
alter pluggable database wed_27388_OnlineQuizSystem_db open;
```

- **Configuration**: After creating the PDB, configure the Service Name using the Net Configuration Assistant.
- **Oracle Enterprise Manager**: Log in with the created username and password, using the pluggable database as the container. The homepage provides access to database management tools.

---
![PDB creation](https://github.com/user-attachments/assets/a881e844-f008-4683-8763-b743bb4a2f3e)


![Image](https://github.com/user-attachments/assets/9ee929bd-6046-479e-81c9-e5d4c4a796ca)
### Phase 5: 📝 Table Implementation and Data Insertion

#### Table Creation
The following SQL code creates the tables for the Online Quiz System, ensuring data integrity with appropriate constraints.

```sql
CREATE TABLE student (
    student_id NUMBER PRIMARY KEY,
    name VARCHAR2(100) NOT NULL,
    email VARCHAR2(100) UNIQUE NOT NULL,
    password VARCHAR2(100) NOT NULL
);

CREATE TABLE teacher (
    teacher_id NUMBER PRIMARY KEY,
    name VARCHAR2(100) NOT NULL,
    email VARCHAR2(100) UNIQUE NOT NULL,
    password VARCHAR2(100) NOT NULL
);

CREATE TABLE quiz (
    quiz_id NUMBER PRIMARY KEY,
    title VARCHAR2(200) NOT NULL,
    created_by NUMBER NOT NULL,
    time_limit NUMBER CHECK (time_limit > 0),
    total_marks NUMBER CHECK (total_marks >= 0),
    CONSTRAINT fk_quiz_teacher FOREIGN KEY (created_by) REFERENCES teacher(teacher_id)
);

CREATE TABLE question (
    question_id NUMBER PRIMARY KEY,
    quiz_id NUMBER NOT NULL,
    question_text VARCHAR2(500) NOT NULL,
    option_a VARCHAR2(200) NOT NULL,
    option_b VARCHAR2(200) NOT NULL,
    option_c VARCHAR2(200),
    option_d VARCHAR2(200),
    correct_option CHAR(1) CHECK (correct_option IN ('A', 'B', 'C', 'D')),
    CONSTRAINT fk_question_quiz FOREIGN KEY (quiz_id) REFERENCES quiz(quiz_id)
);

CREATE TABLE attempt (
    attempt_id NUMBER PRIMARY KEY,
    student_id NUMBER NOT NULL,
    quiz_id NUMBER NOT NULL,
    score NUMBER CHECK (score >= 0),
    attempt_time DATE DEFAULT SYSDATE,
    CONSTRAINT fk_attempt_student FOREIGN KEY (student_id) REFERENCES student(student_id),
    CONSTRAINT fk_attempt_quiz FOREIGN KEY (quiz_id) REFERENCES quiz(quiz_id)
);


#### Data Insertion
Sample data insertion for the **Department** and **Quiz** tables is provided as an example. For complete data insertion details, refer to the repository files.

- **Student Table**:
  ```sql
  INSERT INTO student VALUES (1001, 'Alice Green', 'alice@gmail.com', 'alicepass');
INSERT INTO student VALUES (1002, 'Bob Brown', 'bob@gmail.com', 'bobpass');


- **Quiz Table**:
  ```sql
INSERT INTO quiz VALUES (10, 'Math Quiz 1', 1, 30, 100);
INSERT INTO quiz VALUES (11, 'Science Quiz 1', 2, 20, 50);

  ** Teacher Table**:
  INSERT INTO teacher VALUES (1, 'Mr. Smith', 'smith@school.edu', 'pass123');
INSERT INTO teacher VALUES (2, 'Ms. Jane', 'jane@school.edu', 'pass456');
 
 **Attempt Table**:
 INSERT INTO attempt VALUES (1000, 1001, 10, 90, SYSDATE);
INSERT INTO attempt VALUES (1001, 1002, 11, 45, SYSDATE);

 **Question Table**:
 INSERT INTO question VALUES (100, 10, 'What is 2 + 2?', '3', '4', '5', '6', 'B');
INSERT INTO question VALUES (101, 10, 'What is 10 / 2?', '3', '5', '7', '6', 'B');
INSERT INTO question VALUES (102, 11, 'What planet is known as the Red Planet?', 'Earth', 'Mars', 'Venus', 'Jupiter', 'B');

![teacher](https://github.com/user-attachments/assets/5abe5586-534a-4f97-a518-417e933a32fa)

---

### Phase 6: 📓 Database Interactions and Transactions

#### Database Operations
The system uses **JOIN** operations to retrieve meaningful data, such as student quiz performance.

```sql
SELECT 
    s.student_id,
    s.name AS student_name,
    q.quiz_id,
    q.title AS quiz_title,
    a.score,
    TO_CHAR(a.attempt_time, 'DD-MON-YYYY HH:MI AM') AS attempt_time
FROM 
    student s
JOIN 
    attempt a ON s.student_id = a.student_id
JOIN 
    quiz q ON a.quiz_id = q.quiz_id
ORDER BY 
    s.student_id, a.attempt_time DESC;

```

To call the view:
```sql
SELECT * FROM StudentQuizResults WHERE Student_ID = 1;
```

#### Transaction Management
A transaction ensures data consistency, for example, when recording a quiz submission and updating quiz attempt records.

```sql
BEGIN TRANSACTION;
INSERT INTO SUBMISSION (Submission_ID, Quiz_ID, Student_ID, Submission_Date, Score)
VALUES (1, 1, 1, SYSDATE, 85);
INSERT INTO QUIZ_ATTEMPT (Attempt_ID, Submission_ID, Question_ID, Student_Answer, Is_Correct)
VALUES (1, 1, 1, 'SELECT', 1);
COMMIT;



---

### Phase 7: 🛠️ Advanced Database Programming and Auditing

This phase enhances the Online Quiz System with advanced PL/SQL techniques for efficiency, data integrity, and security.

#### Problem Statement
The system addresses:
1. Enforcing quiz submission rules using **triggers**.
2. Processing quiz results efficiently with **cursors**.
3. Improving modularity with **packages**.
4. Securing sensitive data with **auditing mechanisms**.

#### 1. Triggers
- **BEFORE Trigger**: Validates quiz submission time.
```sql
CREATE OR REPLACE TRIGGER before_submission_insert
BEFORE INSERT ON SUBMISSION
FOR EACH ROW
DECLARE
    v_time_limit QUIZ.Time_Limit%TYPE;
    v_quiz_start DATE;
BEGIN
    SELECT Time_Limit, SYSDATE
    INTO v_time_limit, v_quiz_start
    FROM QUIZ
    WHERE Quiz_ID = :NEW.Quiz_ID;
    
    IF :NEW.Submission_Date > v_quiz_start + (v_time_limit / 1440) THEN
        RAISE_APPLICATION_ERROR(-20001, 'Submission past quiz time limit.');
    END IF;
EXCEPTION
    WHEN NO_DATA_FOUND THEN
        RAISE_APPLICATION_ERROR(-20002, 'Quiz ID not found.');
END;
/
```

#### 2. Cursor Usage
Calculates average scores for each student.

```sql
DECLARE
    CURSOR student_scores IS
        SELECT su.Student_ID, s.Name, AVG(su.Score) AS Avg_Score
        FROM SUBMISSION su
        JOIN STUDENT s ON su.Student_ID = s.Student_ID
        GROUP BY su.Student_ID, s.Name;
    v_student_id STUDENT.Student_ID%TYPE;
    v_student_name STUDENT.Name%TYPE;
    v_avg_score NUMBER;
BEGIN
    OPEN student_scores;
    LOOP
        FETCH student_scores INTO v_student_id, v_student_name, v_avg_score;
        EXIT WHEN student_scores%NOTFOUND;
        DBMS_OUTPUT.PUT_LINE('Student ID: ' || v_student_id || ' | Name: ' || v_student_name || ' | Average Score: ' || v_avg_score);
    END LOOP;
    CLOSE student_scores;
END;
/
```

#### 3. Attributes (%TYPE and %ROWTYPE)
Improves code efficiency.

```sql
DECLARE
    v_student_rec STUDENT%ROWTYPE;
BEGIN
    SELECT * INTO v_student_rec FROM STUDENT WHERE Student_ID = 1;
    DBMS_OUTPUT.PUT_LINE('Student Name: ' || v_student_rec.Name);
END;
/
```

#### 4. Package Development
- **Package Specification**:
```sql
CREATE OR REPLACE PACKAGE quiz_package AS
    PROCEDURE log_audit(p_table_name VARCHAR2, p_action_type VARCHAR2);
    PROCEDURE update_quiz_score(p_submission_id INT);
END quiz_package;
/
```

- **Package Body**:
```sql
CREATE OR REPLACE PACKAGE BODY quiz_package AS
    PROCEDURE log_audit(p_table_name VARCHAR2, p_action_type VARCHAR2) IS
    BEGIN
        INSERT INTO AUDIT_LOG (Table_Name, Action_Type, Changed_By, Change_Date)
        VALUES (p_table_name, p_action_type, USER, SYSDATE);
    END log_audit;

    PROCEDURE update_quiz_score(p_submission_id INT) IS
    BEGIN
        UPDATE SUBMISSION
        SET Score = (SELECT SUM(Is_Correct) * 10 FROM QUIZ_ATTEMPT WHERE Submission_ID = p_submission_id)
        WHERE Submission_ID = p_submission_id;
    END update_quiz_score;
END quiz_package;
/
```

#### 5. Auditing and Restrictions
- **Auditing Trigger**:
```sql
CREATE OR REPLACE TRIGGER audit_sensitive_data
AFTER UPDATE OR DELETE ON STUDENT
FOR EACH ROW
DECLARE
    v_action_type VARCHAR2(10);
BEGIN
    IF DELETING THEN
        v_action_type := 'DELETE';
    ELSIF UPDATING THEN
        v_action_type := 'UPDATE';
    END IF;
    INSERT INTO AUDIT_LOG (Table_Name, Action_Type, Changed_By, Change_Date)
    VALUES ('STUDENT', v_action_type, USER, SYSDATE);
END;
/
```

- **Restriction Example**:
```sql
BEGIN
    IF SYS_CONTEXT('USERENV', 'SESSION_USER') != 'ADMIN_ROLE' THEN
        RAISE_APPLICATION_ERROR(-20003, 'Unauthorized access.');
    END IF;
END;
/
```

#### Scope
- **Triggers**: Enforce quiz rules and automate scoring.
- **Cursors**: Process quiz results efficiently.
- **Packages**: Enhance modularity and reusability.
- **Auditing**: Ensure security and accountability.

---

## Anticipated Benefits
- **Automated Quiz Generation**: Saves time for instructors.
- **Secure & Timed Exams**: Prevents cheating and ensures fairness.
- **Performance Analytics**: Helps teachers track student progress.

For further details, refer to the repository files for screenshots and additional documentation.
Innovations in This Project
Automated Quiz Grading

The system grades quizzes instantly using PL/SQL logic, reducing teacher workload and ensuring consistent scoring.

Security Enforcement via Triggers

Modifications (INSERT, UPDATE, DELETE) are blocked during weekdays and public holidays using a smart PL/SQL trigger based on a holiday reference table.

Integrated Audit Logging System

Every sensitive database operation is tracked using custom triggers and an audit package, enabling full accountability.

MIS Support with Performance Analytics

JOIN operations and reports allow teachers to view top-performing students, average scores, and quiz effectiveness—supporting decision-making.

Modular Architecture with Packages

Reusable PL/SQL packages group procedures and functions for maintainability and cleaner code organization.

🌱 Future Work and Enhancements
Mobile Version of the Quiz System

Develop a mobile-friendly interface or app so students can take quizzes on phones or tablets.

Feedback and Review Module

Allow students to review their answers and receive detailed feedback or explanations for each question after the quiz.

Role-Based Access Control (RBAC)

Implement advanced user roles (Admin, Teacher, Student, Auditor) with separate dashboards and privileges.

Timer and Auto-Submit Feature

Add a countdown timer for timed quizzes that automatically submits the quiz when time expires.

Email Notification System

Notify students of upcoming quizzes and send automatic emails with their scores once quizzes are graded.

Graphical Reporting Dashboard

Use visualization tools (e.g., charts or graphs) for quiz statistics, student performance trends, and overall system activity.

Online Proctoring Support

Add webcam/mic integration or activity monitoring to prevent cheating during online exams.


