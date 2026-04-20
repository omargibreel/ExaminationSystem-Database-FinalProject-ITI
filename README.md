# Examination System Database Project

A comprehensive SQL Server database solution for managing educational examinations, including student enrollment, exam generation, grading, and reporting capabilities.

## Overview

This project implements a complete examination management system using SQL Server stored procedures, featuring 112 stored procedures organized across CRUD operations, business logic, and reporting functionalities [1](#5-0) . The system supports multiple-choice and true/false questions, automated exam generation, student answer submission, and comprehensive grading with SSRS reporting.

## Authors

**Team Leader:** Abdalrhman Mohamed Mohamed

**Development Team:**
- Omar Osama Elsayed Gibreel
- Rodan Mohamed Elsaid  
- Reem Mohamed Rashad
- Hana Adel Elsayed

This project was developed as a final project for ITI by the team members listed above [2](#5-1) .

## Project Architecture & Visual Assets

### System Architecture Overview

```mermaid
graph TB
    subgraph "Presentation Layer"
        UI[User Interface]
        SSRS[SSRS Reports]
        RPT[Report Builder]
    end
    
    subgraph "Business Logic Layer"
        API[Stored Procedure API]
        CRUD[CRUD Operations]
        EXAM[Exam Logic]
        VAL[Validation Engine]
    end
    
    subgraph "Data Layer"
        DB[(SQL Server Database)]
        BACKUP[Backup Files]
    end
    
    UI --> API
    SSRS --> API
    RPT --> API
    
    API --> CRUD
    API --> EXAM
    API --> VAL
    
    CRUD --> DB
    EXAM --> DB
    VAL --> DB
    
    BACKUP --> DB
```

### Examination Workflow

```mermaid
flowchart TD
    START[Start Examination Process]
    
    subgraph "Phase 1: Setup"
        INST[Instructor creates questions]
        COURSE[Assign to courses]
        POOL[Build question pool]
    end
    
    subgraph "Phase 2: Exam Generation"
        GEN[Generate_Exam Procedure]
        RAND[Random question selection]
        SCHED[Schedule exam]
    end
    
    subgraph "Phase 3: Execution"
        ENROLL[Student enrollment]
        ANSWER[Submit answers]
        VALID[Validate time window]
    end
    
    subgraph "Phase 4: Grading & Reporting"
        CORRECT[Auto-correction]
        CALC[Calculate scores]
        REPORT[Generate reports]
    end
    
    START --> INST
    INST --> COURSE
    COURSE --> POOL
    POOL --> GEN
    GEN --> RAND
    RAND --> SCHED
    SCHED --> ENROLL
    ENROLL --> ANSWER
    ANSWER --> VALID
    VALID --> CORRECT
    CORRECT --> CALC
    CALC --> REPORT
```

### Available Visual Assets

📊 **Reports & Documentation**
- 📁 `Reports PDF/` - Contains exported SSRS reports showing actual system output
- 📁 `FreeForm Report/` - SSRS Freeform report layouts and designs
- 📁 `DB Documentation/` - Technical documentation and data dictionaries

🗂️ **Database Schema**
- 📁 `System ERD/Examination System ERD.pdf` - Complete entity-relationship diagram

## Database Structure & ERD

### Entity-Relationship Diagram
The complete ERD diagram is available in the repository at:
- 📁 `System ERD/Examination System ERD.pdf`

This PDF contains the full visual representation of all database tables, relationships, and constraints used in the examination system.

### Database Schema Overview

```mermaid
erDiagram
    %% Core Entities
    Branch ||--o{ Track : "has"
    Track ||--o{ Student : "contains"
    Track ||--o{ Instructor : "supervised by"
    
    Course ||--o{ Topic : "contains"
    Course ||--o{ Question : "has"
    
    Question ||--o{ Choice : "has"
    Question ||--o{ Exam_Question : "used in"
    
    Exam ||--o{ Exam_Question : "contains"
    Exam ||--o{ Student_Exam : "taken by"
    
    Student ||--o{ Student_Exam : "takes"
    Student_Exam ||--o{ Student_Answer : "generates"
    Exam_Question ||--o{ Student_Answer : "answered"
    Choice ||--o{ Student_Answer : "selected"
    
    %% Junction Tables (Many-to-Many)
    Branch ||--o{ Branch_Track : ""
    Track ||--o{ Branch_Track : ""
    
    Track ||--o{ Track_Course : ""
    Course ||--o{ Track_Course : ""
    
    Instructor ||--o{ Instructor_Course : ""
    Course ||--o{ Instructor_Course : ""
    
    Course ||--o{ Course_Topic : ""
    Topic ||--o{ Course_Topic : ""
    
    %% Entity Definitions
    Branch {
        int BranchID PK
        varchar BranchName
        varchar Location
    }
    
    Track {
        int TrackID PK
        varchar TrackName
        int SupervisorID FK
        int BranchID FK
    }
    
    Instructor {
        int InstructorID PK
        varchar FirstName
        varchar LastName
        varchar Email
        varchar Phone
    }
    
    Student {
        int StudentID PK
        varchar FirstName
        varchar LastName
        varchar Email
        varchar Phone
        date DateOfBirth
        int TrackID FK
    }
    
    Course {
        int CourseID PK
        varchar CourseName
        decimal MinPassDegree
    }
    
    Topic {
        int TopicID PK
        varchar TopicName
        varchar Description
    }
    
    Question {
        int QuestionID PK
        varchar QuestionText
        char QuestionType
        decimal QuestionMark
        int CourseID FK
    }
    
    Choice {
        int ChoiceID PK
        varchar ChoiceLabel
        varchar ChoiceText
        bit IsCorrect
        int QuestionID FK
    }
    
    Exam {
        int ExamID PK
        date ExamDate
        time StartTime
        time EndTime
        int TotalMCQQuestions
        int TotalTrueFalseQuestions
        decimal TotalGrade
    }
    
    Student_Exam {
        int StudentExamID PK
        int StudentID FK
        int ExamID FK
        decimal TotalScore
        decimal Percentage
    }
    
    Student_Answer {
        int StudentAnswerID PK
        int StudentExamID FK
        int QuestionID FK
        int AnswerID FK
        bit IsCorrect
        decimal Mark
    }
```

### Repository Structure

```
├── DB Backup/                          # Database backup file
├── DB Documentation/                   # Technical documentation
├── Stored Procedure Code/              # All T-SQL stored procedures
│   ├── Main_Entities_Stored_Procedures.sql
│   ├── Many_To_Many_Entities_Stored_Procedures.sql
│   ├── Exam_Generation_Answers_Correction_Stored_Procedures.sql
│   ├── Reports_Stored_Procedures.sql
│   └── please_start_with_me.sql        # Simulation script
├── System ERD/                         # Entity-Relationship Diagram
│   └── Examination System ERD.pdf      # Complete database schema visualization
├── Reports PDF/                        # Generated report exports
└── FreeForm Report/                    # SSRS Freeform report layouts
```

## Quick Start

### 1. Database Restoration

1. Open SQL Server Management Studio (SSMS)
2. Right-click **Databases** → **Restore Database**
3. Select **Device** and locate: `DB Backup/db35423_custom_31.12.2025_639027727339467022.bak`
4. Click **OK** to restore

### 2. Run Simulation

Execute the simulation script to populate test data and verify functionality:

```sql
-- Execute the complete simulation
-- This creates 5 exams, enrolls 21 students, and submits answers
EXEC Stored Procedures Code/please_start_with_me.sql
```

The simulation creates:
- 5 exams for different courses (C#, ASP.NET Core, SQL Server, JavaScript, HTML & CSS) [3](#5-2) 
- 21 student enrollments with 210 answer submissions [4](#5-3) 
- Automated grading and report generation

## Core Features

### Database Schema
- **9 Core Tables**: Branch, Track, Instructor, Student, Course, Topic, Question, Choice, Exam
- **8 Junction Tables**: Managing many-to-many relationships
- **User-Defined Table Types**: For bulk data operations (e.g., `ChoiceTableType`)

### Stored Procedure Categories

1. **Main Entity CRUD** (36 procedures)
   - Add/Update/Delete/Get operations for core entities
   - Example: `AddStudent`, `UpdateBranch`, `DeleteCourse` [5](#5-4) 

2. **Many-to-Many Relations** (58 procedures)
   - Managing junction tables and complex relationships
   - Example: `AddStudentExam`, `DeleteTracksByBranchID` [6](#5-5) 

3. **Exam Business Logic** (5 procedures)
   - `Generate_Exam`: Creates randomized exams from course question pools [7](#5-6) 
   - `Student_Exam_Correction`: Automated grading system
   - `AddStudentAnswer`: Answer submission with validation

4. **Reporting Procedures** (6 procedures)
   - SSRS-compatible procedures for generating reports
   - Student grades, course topics, exam questions, etc. [8](#5-7) 

### Key Business Logic

- **Exam Generation**: Randomly selects questions using `NEWID()` to ensure unique exams [9](#5-8) 
- **Validation Rules**: Age constraints (18-45), email format, temporal guards for exam timing
- **Cascade Deletion**: Proper handling of related records with referential integrity

## Reporting System

### Report Generation Architecture

```mermaid
graph LR
    subgraph "Data Sources"
        DB[(Database Tables)]
        SP[Stored Procedures]
    end
    
    subgraph "Reporting Tools"
        SSRS[SQL Server Reporting Services]
        RB[Report Builder]
        FR[Freeform Reports]
    end
    
    subgraph "Output Formats"
        PDF[PDF Reports]
        WEB[Web Reports]
        EXPORT[Data Exports]
    end
    
    DB --> SP
    SP --> SSRS
    SP --> RB
    SP --> FR
    
    SSRS --> PDF
    SSRS --> WEB
    RB --> PDF
    FR --> PDF
    FR --> EXPORT
```

The system integrates with SQL Server Reporting Services (SSRS) and Microsoft Report Builder to generate 6 primary reports [10](#5-9) :

1. Student information by department
2. Student grades across all courses
3. Instructor course assignments and student counts
4. Course topics listing
5. Exam questions and choices
6. Student exam answers with correct choices

## Data Validation

The system enforces business rules through comprehensive validation:

- **Age Validation**: Students must be between 18-45 years old [11](#5-10) 
- **Name Format**: Prevents numeric characters in names
- **Email Pattern**: Validates email format using pattern matching
- **Temporal Constraints**: Prevents exam submission outside scheduled times
- **Question Pool Validation**: Ensures sufficient questions before exam generation

## Technical Specifications

- **Database**: SQL Server
- **Total Stored Procedures**: 112
- **Reporting**: SSRS, Microsoft Report Builder, Freeform Reports
- **Backup Format**: `.bak` file for full environment restoration
- **Testing**: Comprehensive simulation script for end-to-end validation

## Notes

- The `please_start_with_me.sql` script is essential for quick testing and demonstration of all system features
- All stored procedures include comprehensive error handling with `RAISERROR` and `THROW` patterns
- The system uses User-Defined Table Types (UDTT) for efficient bulk operations
- Referential integrity is maintained through proper cascade deletion logic and foreign key constraints
- For the complete visual ERD diagram, refer to `System ERD/Examination System ERD.pdf` in the repository
- Sample reports and layouts are available in `Reports PDF/` and `FreeForm Report/` directories

### Citations

**File:** IMPORTANT NOTE.txt (L2-2)
```text
We used "SSRS , Microsoft Report Builder, Microsoft Freeform Report" tool to generate our Reports
```

**File:** IMPORTANT NOTE.txt (L19-19)
```text
*** Total of 112 Stored Procedures ***
```

**File:** IMPORTANT NOTE.txt (L23-23)
```text
5. "please_start_with_me" file : this has a simulation of creating 5 exams for 5 different couses , and assigning 21 students to these exams (each exam has 10 questions) , resulting in 210 rows for student answers + 20 more for the the same student to test the report of getting his degrees in multiple courses. it also has exam correction for some of these students and model answer for exams. IF YOU WANT A QUICK TEST FOR THE REPORT STORED PROCEDURES YOU SHOULD START WITH THIS FILE :)
```

**File:** Team Names.txt (L5-9)
```text
- Reem Mohamed Rashad
- Rodan Mohamed Elsaid
- Hana Adel Elsayed
- Omar Osama Elsayed
- Abdalrhman Mohamed Mohamed
```

**File:** Stored Procedures Code/Main_Entities_Stored_Procedures.sql (L134-204)
```sql
CREATE PROCEDURE AddStudent
    @FirstName VARCHAR(50),
    @LastName VARCHAR(50),
    @Email VARCHAR(100),
    @Phone VARCHAR(20),
    @DateOfBirth DATE,
    @TrackID INT
AS
BEGIN
    --SET NOCOUNT ON;

    -- 1. Validate first and last names (letters + special chars, no numbers)
    IF @FirstName LIKE '%[0-9]%' OR @LastName LIKE '%[0-9]%'
    BEGIN
        RAISERROR('First and Last names cannot contain numbers.', 16, 1);
        RETURN;
    END;

    -- 2. Check that Track exists
    IF NOT EXISTS (
        SELECT 1 FROM Track
        WHERE TrackID = @TrackID
    )
    BEGIN
        RAISERROR('Track does not exist.', 16, 1);
        RETURN;
    END;

    -- 3. Validate age between 18 and 45
    DECLARE @Age INT;
    SET @Age = DATEDIFF(YEAR, @DateOfBirth, GETDATE())
               - CASE 
                   WHEN MONTH(@DateOfBirth) > MONTH(GETDATE())
                     OR (MONTH(@DateOfBirth) = MONTH(GETDATE()) AND DAY(@DateOfBirth) > DAY(GETDATE()))
                   THEN 1 ELSE 0 END;

    IF @Age < 18 OR @Age > 45
    BEGIN
        RAISERROR('Student age must be between 18 and 45.', 16, 1);
        RETURN;
    END;

    -- 4. Validate email format (basic)
    IF @Email NOT LIKE '%_@__%.__%'
    BEGIN
        RAISERROR('Invalid email format.', 16, 1);
        RETURN;
    END;

    -- 5. Validate email uniqueness
    IF EXISTS (
        SELECT 1
        FROM Student
        WHERE Email = @Email
    )
    BEGIN
        RAISERROR('Email already exists.', 16, 1);
        RETURN;
    END;

    -- 6. Validate phone format (digits only, optional +, length 10-15)
    IF @Phone LIKE '%[^0-9+]%' OR LEN(@Phone) < 10 OR LEN(@Phone) > 15
    BEGIN
        RAISERROR('Phone number must contain only digits (optionally +) and be 10-15 characters long.', 16, 1);
        RETURN;
    END;

    -- 7. Insert student
    INSERT INTO Student (FirstName, LastName, Email, Phone, DateOfBirth, TrackID)
    VALUES (@FirstName, @LastName, @Email, @Phone, @DateOfBirth, @TrackID);
END;
```

**File:** Stored Procedures Code/Many_To_Many_Entities_Stored_Procedures.sql (L684-706)
```sql


------------------------------------------------- Student Answer
-- 1. Get all Student-Answer details
CREATE PROCEDURE GetAllStudentAnswers
AS
BEGIN
    --SET NOCOUNT ON;

    IF NOT EXISTS (SELECT 1 FROM Student_Answer)
        THROW 50001, 'No Student-Answer records found.', 1;

    SELECT 
        sa.StudentAnswerID,
        s.StudentID,
        s.FirstName + ' ' + s.LastName AS StudentName,
        e.ExamID,
        q.QuestionID,
        q.QuestionText,
        ch.ChoiceID,
        ch.ChoiceLabel,
        ch.ChoiceText,
        sa.IsCorrect,
```
