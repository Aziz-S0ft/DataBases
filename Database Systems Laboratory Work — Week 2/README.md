# Database Systems Laboratory Work — Week 2

## Part 1: Key Identification Exercises

### Task 1.1: Superkey and Candidate Key Analysis

#### Relation A: Employee
`Employee(EmpID, SSN, Email, Phone, Name, Department, Salary)`

1. **6 Different Superkeys:**
   * `{EmpID}`
   * `{SSN}`
   * `{Email}`
   * `{phone}`
   * `{EmpID, Name}`
   * `{SSN, Department}`

2. **Candidate Keys:**
   * `{EmpID}`
   * `{SSN}`
   * `{Email}`
   * `{Phone}` (based on the sample data where all numbers are unique)

3. **Chosen Primary Key & Justification:**
   * **Selected PK:** `{EmpID}`
   * **Why:** It is a minimal, unique, stable surrogate key designed specifically for internal database management. Unlike `SSN`, it does not expose sensitive personal information, and unlike `Email` or `Phone`, it never changes.

4. **Can two employees have the same phone number?**
   * Based strictly on the sample data, every employee has a unique phone number. However, in real-world domain logic, employees could share a work/landline phone or lack a contact number entirely (`NULL`). Therefore, `Phone` should not be constrained as a primary key in production.

---

#### Relation B: Course Registration
`Registration(StudentID, CourseCode, Section, Semester, Year, Grade, Credits)`

1. **Minimum attributes for Primary Key:**
   * `{StudentID, CourseCode, Section, Semester, Year}`

2. **Attribute Necessity Explanation:**
   * `StudentID`: Identifies which student is registering.
   * `CourseCode`: Identifies the subject.
   * `Section`: Distinguishes between multiple class offerings of the same course.
   * `Semester` & `Year`: Allow a student to retake the same course/section in different terms (Business Rule 1).

3. **Additional Candidate Keys:**
   * None. The composite set `{StudentID, CourseCode, Section, Semester, Year}` is the sole minimal candidate key.

---

### Task 1.2: Foreign Key Design

#### Given Schema:
* `Student(StudentID, Name, Email, Major, AdvisorID)`
* `Professor(ProfID, Name, Department, Salary)`
* `Course(CourseID, Title, Credits, DepartmentCode)`
* `Department(DeptCode, DeptName, Budget, ChairID)`
* `Enrollment(StudentID, CourseID, Semester, Grade)`

#### Identified Foreign Keys:
1. `Student.AdvisorID` → references `Professor.ProfID`
2. `Course.DepartmentCode` → references `Department.DeptCode`
3. `Department.ChairID` → references `Professor.ProfID`
4. `Enrollment.StudentID` → references `Student.StudentID`
5. `Enrollment.CourseID` → references `Course.CourseID`

---

# Part 2: ER Diagram Construction

## Task 2.1: Hospital Management System

## 1. Entities & Classification

### Strong Entities

* **Patient**
* **Doctor**
* **Department**
* **Appointment**
* **Prescription**

### Weak Entity

* **Hospital Room** — weak entity because a room number is unique only within a department.

  * Partial Key: `RoomNumber`
  * Full identification: `(DeptCode, RoomNumber)`

---

## 2. Attribute Classification

### Patient

* `PatientID` — Primary Key, Simple
* `Name` — Simple
* `Birthdate` — Simple
* `Address` — Composite

  * `Street`
  * `City`
  * `State`
  * `Zip`
* `Phone` — Multivalued
* `InsuranceInfo` — Simple

### Doctor

* `DoctorID` — Primary Key, Simple
* `Name` — Simple
* `Specialization` — Multivalued
* `Phone` — Simple
* `OfficeLocation` — Simple

### Department

* `DeptCode` — Primary Key, Simple
* `Name` — Simple
* `Location` — Simple

### Hospital Room

* `RoomNumber` — Partial Key, Simple

### Appointment

* `AppointmentID` — Primary Key, Simple
* `DateTime` — Simple
* `Purpose` — Simple
* `Notes` — Simple

### Prescription

* `PrescriptionID` — Primary Key, Simple
* `Medication` — Simple
* `Dosage` — Simple
* `Instructions` — Simple

---

## 3. Relationships & Cardinalities

* **Department — Hospital Room:** **1:N**

  * One department has many rooms.
  * Each room belongs to one department.

* **Department — Doctor:** **1:N**

  * One department can have many doctors.
  * Each doctor belongs to one department.

* **Patient — Appointment:** **1:N**

  * One patient can have many appointments.
  * Each appointment is for one patient.

* **Doctor — Appointment:** **1:N**

  * One doctor can handle many appointments.
  * Each appointment is handled by one doctor.

* **Patient — Prescription:** **1:N**

  * One patient can have many prescriptions.
  * Each prescription is prescribed to one patient.

* **Doctor — Prescription:** **1:N**

  * One doctor can prescribe many prescriptions.
  * Each prescription is prescribed by one doctor.

* **Appointment — Prescription:** **1:N**

  * One appointment can generate multiple prescriptions.
  * Each prescription is associated with one appointment.

---

## 4. Primary Keys

* **Patient:** `PatientID`
* **Doctor:** `DoctorID`
* **Department:** `DeptCode`
* **Appointment:** `AppointmentID`
* **Prescription:** `PrescriptionID`
* **Hospital Room:** `(DeptCode, RoomNumber)`

---

## 5. Summary

The ER diagram contains **5 strong entities** and **1 weak entity**:

* Patient
* Doctor
* Department
* Appointment
* Prescription
* Hospital Room (Weak Entity)

The main relationships are:

```text
Department 1:N Hospital Room
Department 1:N Doctor
Patient 1:N Appointment
Doctor 1:N Appointment
Patient 1:N Prescription
Doctor 1:N Prescription
Appointment 1:N Prescription
```


---

# Task 2.2: E-commerce Platform

## 1. Weak Entity & Justification

* **Weak Entity:** `OrderItem`
* **Justification:** An `OrderItem` cannot exist independently without a parent `Order`. Its existence depends on a specific `Order`.
* `ItemNumber` is the **partial key** of `OrderItem`.
* The complete identification of an `OrderItem` is **(OrderID, ItemNumber)**.

---

## 2. Many-to-Many (M:N) Relationship with Attributes

* **Relationship:** `Order` ↔ `Product`
* **Cardinality:** **M:N**
* The M:N relationship is resolved using the associative entity `OrderItem`.
* **Attributes of `OrderItem`:**

  * `Quantity`
  * `UnitPriceAtOrderTime`

### Explanation

One `Order` can contain multiple `Products`, and one `Product` can appear in multiple `Orders`.

Therefore:

**Order M:N Product**

The `OrderItem` entity resolves this M:N relationship and stores information specific to each product in an order, such as the quantity ordered and the price at the time of purchase.

---

## Part 4: Normalization Workshop

# Task 4.1: Denormalized Table Analysis

Given relation:

`StudentProject(StudentID, StudentName, StudentMajor, ProjectID, ProjectTitle, ProjectType, SupervisorID, SupervisorName, SupervisorDept, Role, HoursWorked, StartDate, EndDate)`

## 1. Functional Dependencies (FDs)

* **FD1:** `StudentID → StudentName, StudentMajor`
* **FD2:** `ProjectID → ProjectTitle, ProjectType, SupervisorID, StartDate, EndDate`
* **FD3:** `SupervisorID → SupervisorName, SupervisorDept`
* **FD4:** `{StudentID, ProjectID} → Role, HoursWorked`

The primary key is `{StudentID, ProjectID}` because a student can participate in multiple projects, and a project can have multiple students.

---

## 2. Redundancy & Anomalies

### Redundancy

Student information, project information, and supervisor information can be repeated across multiple rows.

For example, if several students work on the same project, the following project information is repeated:

* `ProjectTitle`
* `ProjectType`
* `SupervisorID`
* `SupervisorName`
* `SupervisorDept`
* `StartDate`
* `EndDate`

### Update Anomaly

If a supervisor changes their department, the `SupervisorDept` must be updated in multiple rows. If some rows are not updated, inconsistent data will exist.

### Insertion Anomaly

A new student who has not been assigned to any project cannot be easily added because the relation requires project-related information.

Similarly, adding a new project without assigning any student would require `StudentID` to be NULL.

### Deletion Anomaly

If the only student assigned to a project is deleted, all information about that project and its supervisor may also be lost.

---

## 3. 1NF and 2NF

### 1NF

The relation is already in **1NF** because all attributes contain atomic values and there are no repeating groups.

### Primary Key

The primary key is:

`{StudentID, ProjectID}`

### Partial Dependencies

There are two partial dependencies:

* `StudentID → StudentName, StudentMajor`
* `ProjectID → ProjectTitle, ProjectType, SupervisorID, StartDate, EndDate`

These attributes depend only on part of the composite primary key.

However:

`{StudentID, ProjectID} → Role, HoursWorked`

depends on the entire primary key, so it is not a partial dependency.

### 2NF Decomposition

Remove the partial dependencies:

**Student**

`Student(StudentID, StudentName, StudentMajor)`

**Project**

`Project(ProjectID, ProjectTitle, ProjectType, SupervisorID, SupervisorName, SupervisorDept, StartDate, EndDate)`

**ProjectAssignment**

`ProjectAssignment(StudentID, ProjectID, Role, HoursWorked)`

At this stage, the relations are in **2NF**, but `Project` still contains a transitive dependency.

---

## 4. 3NF Decomposition

### Transitive Dependency

In the `Project` relation:

`ProjectID → SupervisorID`

and:

`SupervisorID → SupervisorName, SupervisorDept`

Therefore:

`ProjectID → SupervisorName, SupervisorDept`

This is a **transitive dependency**.

To achieve 3NF, supervisor information must be separated into its own relation.

### Final 3NF Relations

**1. Student**

`Student(StudentID PK, StudentName, StudentMajor)`

**2. Supervisor**

`Supervisor(SupervisorID PK, SupervisorName, SupervisorDept)`

**3. Project**

`Project(ProjectID PK, ProjectTitle, ProjectType, SupervisorID FK, StartDate, EndDate)`

**4. ProjectAssignment**

`ProjectAssignment(StudentID PK/FK, ProjectID PK/FK, Role, HoursWorked)`

### Foreign Keys

* `Project.SupervisorID → Supervisor.SupervisorID`
* `ProjectAssignment.StudentID → Student.StudentID`
* `ProjectAssignment.ProjectID → Project.ProjectID`

---

## Final Answer

The final database in **3NF** consists of four relations:

1. `Student(StudentID PK, StudentName, StudentMajor)`
2. `Supervisor(SupervisorID PK, SupervisorName, SupervisorDept)`
3. `Project(ProjectID PK, ProjectTitle, ProjectType, SupervisorID FK, StartDate, EndDate)`
4. `ProjectAssignment(StudentID PK/FK, ProjectID PK/FK, Role, HoursWorked)`

---
### Task 4.2: Advanced Normalization

Given relation:

`CourseSchedule(StudentID, StudentMajor, CourseID, CourseName, InstructorID, InstructorName, TimeSlot, Room, Building)`

#### 1. Primary Key & Functional Dependencies

A student can be enrolled in multiple course sections.

A course section is identified by its `CourseID` and `TimeSlot`. Therefore, to identify one enrollment, we need:

**Primary Key:** `{StudentID, CourseID, TimeSlot}`

Functional Dependencies:

- FD1: `StudentID → StudentMajor`
- FD2: `CourseID → CourseName`
- FD3: `InstructorID → InstructorName`
- FD4: `Room → Building`
- FD5: `{CourseID, TimeSlot} → InstructorID, Room`

The primary key determines all remaining attributes:

- `{StudentID, CourseID, TimeSlot} → StudentMajor, CourseName, InstructorID, InstructorName, Room, Building`

#### 2. BCNF Check

The original relation is **not in BCNF**.

BCNF requires that the left-hand side of every non-trivial functional dependency is a superkey.

However:

- `StudentID` is not a superkey.
- `CourseID` is not a superkey.
- `InstructorID` is not a superkey.
- `Room` is not a superkey.
- `{CourseID, TimeSlot}` is not a superkey because it does not identify a particular student.

Therefore, the relation violates BCNF.

#### 3. BCNF Decomposition

We decompose the relation into the following BCNF relations:

1. `Student(**StudentID**, StudentMajor)`

2. `Course(**CourseID**, CourseName)`

3. `Instructor(**InstructorID**, InstructorName)`

4. `RoomLocation(**Room**, Building)`

5. `ClassSection(**CourseID, TimeSlot**, InstructorID, Room)`

6. `StudentEnrollment(**StudentID, CourseID, TimeSlot**)`

Where:

- `StudentID` in `StudentEnrollment` references `Student(StudentID)`
- `CourseID` in `StudentEnrollment` references `Course(CourseID)`
- `(CourseID, TimeSlot)` in `StudentEnrollment` references `ClassSection(CourseID, TimeSlot)`
- `InstructorID` in `ClassSection` references `Instructor(InstructorID)`
- `Room` in `ClassSection` references `RoomLocation(Room)`

#### 4. Why the Decomposition is in BCNF

Each relation has a determinant that is a candidate key:

- `StudentID → StudentMajor` in `Student`
- `CourseID → CourseName` in `Course`
- `InstructorID → InstructorName` in `Instructor`
- `Room → Building` in `RoomLocation`
- `{CourseID, TimeSlot} → InstructorID, Room` in `ClassSection`

Therefore, all decomposed relations satisfy BCNF.

#### 5. Information Loss Analysis

The decomposition is **lossless** because the relations can be joined using their primary keys and foreign keys to reconstruct the original information.

For example:

`StudentEnrollment`
→ identifies the student and course section

`ClassSection`
→ gives the instructor, time, and room

`Course`
→ gives the course name

`Instructor`
→ gives the instructor name

`RoomLocation`
→ gives the building

`Student`
→ gives the student's major

Therefore, no original information is lost through the decomposition.

### Final BCNF Schema

- `Student(StudentID PK, StudentMajor)`
- `Course(CourseID PK, CourseName)`
- `Instructor(InstructorID PK, InstructorName)`
- `RoomLocation(Room PK, Building)`
- `ClassSection(CourseID PK, TimeSlot PK, InstructorID, Room)`
- `StudentEnrollment(StudentID PK, CourseID PK, TimeSlot PK)`
---

# Part 5: Design Challenge

## Task 5.1: Real-World Application

### 1. ER Diagram Design

The university needs a system to manage student clubs and organizations, memberships, events, attendance, officers, faculty advisors, room reservations, budgets, and expenses.

### Main Entities

#### Student
- StudentID (PK)
- Name
- Email

#### Club
- ClubID (PK)
- ClubName
- Description
- Budget

#### Faculty
- FacultyID (PK)
- Name
- Department
- Email

#### Membership
- StudentID (PK, FK)
- ClubID (PK, FK)
- JoinDate

Membership is an associative entity because students can join multiple clubs and each club can have multiple students.

#### OfficerPosition
- PositionID (PK)
- PositionName

Examples:
- President
- Treasurer
- Secretary

#### ClubOfficer
- StudentID (PK, FK)
- ClubID (PK, FK)
- PositionID (FK)
- StartDate
- EndDate

ClubOfficer connects students with clubs and officer positions.

#### Event
- EventID (PK)
- ClubID (FK)
- EventName
- EventDate
- Description

One club can organize many events.

#### Attendance
- StudentID (PK, FK)
- EventID (PK, FK)
- AttendanceStatus

Attendance is an associative entity between Student and Event.

#### Room
- RoomID (PK)
- Building
- RoomNumber
- Capacity

#### RoomReservation
- ReservationID (PK)
- EventID (FK)
- RoomID (FK)
- StartTime
- EndTime

#### Expense
- ExpenseID (PK)
- ClubID (FK)
- ExpenseDate
- Amount
- Description

---

### Relationships and Cardinalities

1. Student M:N Club
   - Resolved by Membership.

2. Student M:N Event
   - Resolved by Attendance.

3. Club 1:N Event
   - One club can organize many events.
   - Each event belongs to one club.

4. Faculty 1:N Club
   - Each club has exactly one faculty advisor.
   - One faculty advisor can advise multiple clubs.

5. Club M:N Student through ClubOfficer
   - A student can hold an officer position in a club.

6. OfficerPosition 1:N ClubOfficer
   - One position type can be assigned to many officers.

7. Event 1:N RoomReservation
   - An event can have a room reservation.

8. Room 1:N RoomReservation
   - A room can be reserved for many events at different times.

9. Club 1:N Expense
   - One club can have many expenses.

---

## 2. Normalized Relational Schema

### Student

`Student(StudentID PK, Name, Email)`

### Club

`Club(ClubID PK, ClubName, Description, Budget, FacultyID FK)`

### Faculty

`Faculty(FacultyID PK, Name, Department, Email)`

### Membership

`Membership(StudentID PK/FK, ClubID PK/FK, JoinDate)`

Foreign Keys:
- `StudentID → Student.StudentID`
- `ClubID → Club.ClubID`

### OfficerPosition

`OfficerPosition(PositionID PK, PositionName)`

### ClubOfficer

`ClubOfficer(StudentID PK/FK, ClubID PK/FK, PositionID FK, StartDate, EndDate)`

Foreign Keys:
- `StudentID → Student.StudentID`
- `ClubID → Club.ClubID`
- `PositionID → OfficerPosition.PositionID`

### Event

`Event(EventID PK, ClubID FK, EventName, EventDate, Description)`

Foreign Key:
- `ClubID → Club.ClubID`

### Attendance

`Attendance(StudentID PK/FK, EventID PK/FK, AttendanceStatus)`

Foreign Keys:
- `StudentID → Student.StudentID`
- `EventID → Event.EventID`

### Room

`Room(RoomID PK, Building, RoomNumber, Capacity)`

### RoomReservation

`RoomReservation(ReservationID PK, EventID FK, RoomID FK, StartTime, EndTime)`

Foreign Keys:
- `EventID → Event.EventID`
- `RoomID → Room.RoomID`

### Expense

`Expense(ExpenseID PK, ClubID FK, ExpenseDate, Amount, Description)`

Foreign Key:
- `ClubID → Club.ClubID`

---

## 3. Design Decision

One design decision is how to represent club officer positions.

There are two possible approaches:

### Option 1
Store the position directly in the ClubOfficer table:

`ClubOfficer(StudentID, ClubID, PositionName, StartDate, EndDate)`

### Option 2
Create a separate OfficerPosition entity:

`OfficerPosition(PositionID, PositionName)`

I chose **Option 2** because it avoids repeating position names such as "President", "Treasurer", and "Secretary".

It also makes the database more normalized and allows the university to manage a standard list of officer positions.

---

## 4. Example Queries

### Query 1
"Find all students who are officers in the Computer Science Club."

### Query 2
"List all events scheduled for next week with their room reservations, including the building and room number."

### Query 3
"Show the total expenses for each club during the current semester."
