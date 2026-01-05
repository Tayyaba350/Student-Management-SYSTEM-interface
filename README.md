# Student-Management-SYSTEM-interface

 
DATABASE PROJECT
PROJECT NAME:
STUDENT MANAGEMENT SYSTEM
Project Code:(ALL STEPS) 


CREATE DATABASE
CREATE DATABASE StudentManagementSystem;
USE StudentManagementSystem;
________________________________________
CORE TABLES 
Department
CREATE TABLE Department (
  DepartmentID INT PRIMARY KEY AUTO_INCREMENT,
  DepartmentName VARCHAR(100),
  Faculty VARCHAR(100)
);

Course
CREATE TABLE Course (
  CourseID INT PRIMARY KEY AUTO_INCREMENT,
  CourseName VARCHAR(100),
  Credits INT,
  DepartmentID INT,
  FOREIGN KEY (DepartmentID) REFERENCES Department(DepartmentID)
);

Student
CREATE TABLE Student (
  StudentID INT PRIMARY KEY AUTO_INCREMENT,
  Name VARCHAR(100),
  Age INT,
  Gender VARCHAR(10),
  Email VARCHAR(100),
  Phone VARCHAR(20),
  DepartmentID INT,
  Status VARCHAR(20),
  FOREIGN KEY (DepartmentID) REFERENCES Department(DepartmentID)
);
________________________________________
ACADEMIC TABLES

Enrollment
CREATE TABLE Enrollment (
  EnrollmentID INT PRIMARY KEY AUTO_INCREMENT,
  StudentID INT,
  CourseID INT,
  Semester VARCHAR(20),
  FOREIGN KEY (StudentID) REFERENCES Student(StudentID),
  FOREIGN KEY (CourseID) REFERENCES Course(CourseID)
);

Exam
CREATE TABLE Exam (
  ExamID INT PRIMARY KEY AUTO_INCREMENT,
  CourseID INT,
  ExamType VARCHAR(50),
  ExamDate DATE,
  FOREIGN KEY (CourseID) REFERENCES Course(CourseID)
);

Result
CREATE TABLE Result (
  ResultID INT PRIMARY KEY AUTO_INCREMENT,
  StudentID INT,
  ExamID INT,
  Marks INT,
  Grade VARCHAR(5),
  FOREIGN KEY (StudentID) REFERENCES Student(StudentID),
  FOREIGN KEY (ExamID) REFERENCES Exam(ExamID)
);
________________________________________

FINANCIAL & ADMIN TABLES
Fee
CREATE TABLE Fee (
  FeeID INT PRIMARY KEY AUTO_INCREMENT,
  StudentID INT,
  Amount DECIMAL(10,2),
  Status VARCHAR(20),
  DueDate DATE,
  FOREIGN KEY (StudentID) REFERENCES Student(StudentID)
);

Scholarship
CREATE TABLE Scholarship (
  ScholarshipID INT PRIMARY KEY AUTO_INCREMENT,
  Name VARCHAR(100),
  Amount DECIMAL(10,2)
);

StudentScholarship
CREATE TABLE StudentScholarship (
  ID INT PRIMARY KEY AUTO_INCREMENT,
  StudentID INT,
  ScholarshipID INT,
  FOREIGN KEY (StudentID) REFERENCES Student(StudentID),
  FOREIGN KEY (ScholarshipID) REFERENCES Scholarship(ScholarshipID)
);
________________________________________

SYSTEM USERS (Login GUI)
CREATE TABLE User (
  UserID INT PRIMARY KEY AUTO_INCREMENT,
  Username VARCHAR(50),
  Password VARCHAR(50),
  Role VARCHAR(20)
);
________________________________________

TRIGGER (AUTO GRADE CALCULATION)
DELIMITER //
CREATE TRIGGER CalculateGrade
BEFORE INSERT ON Result
FOR EACH ROW
BEGIN
  IF NEW.Marks >= 85 THEN SET NEW.Grade = 'A';
  ELSEIF NEW.Marks >= 70 THEN SET NEW.Grade = 'B';
  ELSEIF NEW.Marks >= 50 THEN SET NEW.Grade = 'C';
  ELSE SET NEW.Grade = 'F';
  END IF;
END;
//
DELIMITER ;
________________________________________


VIEW (FOR GUI DASHBOARD)
CREATE VIEW StudentPerformanceBoard AS
SELECT 
  S.Name AS Student_Name,
  C.CourseName,
  R.Marks,
  R.Grade
FROM Student S
JOIN Result R ON S.StudentID = R.StudentID
JOIN Exam E ON R.ExamID = E.ExamID
JOIN Course C ON E.CourseID = C.CourseID;
________________________________________


SAMPLE DATA (IMPORTANT FOR GUI DEMO)
INSERT INTO Department (DepartmentName, Faculty) VALUES
('Computer Science','Engineering'),
('Business','Management');

INSERT INTO Course (CourseName, Credits, DepartmentID) VALUES
('Database Systems',3,1),
('Software Engineering',4,1),
('Accounting',3,2);

INSERT INTO Student (Name, Age, Gender, Email, Phone, DepartmentID, Status) VALUES
('Ali Khan',21,'Male','ali@gmail.com','0300123456',1,'Active'),
('Sara Ahmed',20,'Female','sara@gmail.com','0300987654',2,'Active');

INSERT INTO User (Username, Password, Role)
VALUES ('admin','admin','Administrator');
________________________________________
GUI-READY CRUD QUERIES

ADD STUDENT
INSERT INTO Student (Name,Age,Gender,Email,Phone,DepartmentID,Status)
VALUES (?,?,?,?,?,?,'Active');

VIEW STUDENTS (TABLE)
SELECT S.StudentID, S.Name, D.DepartmentName, S.Email, S.Status
FROM Student S
JOIN Department D ON S.DepartmentID = D.DepartmentID;
UPDATE STUDENT
UPDATE Student
SET Email=?, Phone=?, Status=?
WHERE StudentID=?;

DELETE STUDENT
DELETE FROM Student WHERE StudentID=?;
________________________________________

REPORT & DASHBOARD QUERIES
Students per Department
SELECT D.DepartmentName, COUNT(S.StudentID) AS TotalStudents
FROM Student S
JOIN Department D ON S.DepartmentID = D.DepartmentID
GROUP BY D.DepartmentName;


Fee Defaulters
SELECT S.Name, F.Amount, F.DueDate
FROM Fee F
JOIN Student S ON F.StudentID = S.StudentID
WHERE F.Status = 'Unpaid';

Scholarship Holders
SELECT S.Name, Sc.Name AS Scholarship, Sc.Amount
FROM StudentScholarship SS
JOIN Student S ON SS.StudentID = S.StudentID
JOIN Scholarship Sc ON SS.ScholarshipID = Sc.ScholarshipID;

Result Report
SELECT * FROM StudentPerformanceBoard;
