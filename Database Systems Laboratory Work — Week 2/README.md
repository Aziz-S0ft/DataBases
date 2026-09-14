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

## Part 2: ER Diagram Construction

### Task 2.1: Hospital Management System

1. **Entities & Classification:**
   * **Strong Entities:** `Patient`, `Doctor`, `Department`
   * **Weak Entities:** 
     * `Hospital Room` (Weak entity; identified relative to `Department`). Partial Key: `RoomNumber`.
     * `Appointment` (Associative / Weak entity connecting `Patient` and `Doctor`).
     * `Prescription` (Weak entity dependent on `Appointment`).

2. **Attribute Classification:**
   * **`Patient`:** `PatientID` (PK, Simple), `Name` (Simple), `Birthdate` (Simple), `Address` (Composite: Street, City, State, Zip), `Phone` (Multivalued), `InsuranceInfo` (Simple).
   * **`Doctor`:** `DoctorID` (PK, Simple), `Name` (Simple), `Specialization` (Multivalued), `Phone` (Simple), `OfficeLocation` (Simple).
   * **`Department`:** `DeptCode` (PK, Simple), `Name` (Simple), `Location` (Simple).
   * **`Hospital Room`:** `RoomNumber` (Partial Key, Simple).
   * **`Appointment`:** `AppointmentID` (PK), `DateTime` (Simple), `Purpose` (Simple), `Notes` (Simple).
   * **`Prescription`:** `PrescriptionID` (PK), `Medication` (Simple), `Dosage` (Simple), `Instructions` (Simple).

3. **Relationships & Cardinalities:**
   * `Department` to `Hospital Room`: **1:N** (One department has many rooms; a room belongs to one department).
   * `Department` to `Doctor`: **1:N** (One department employs many doctors).
   * `Patient` to `Appointment`: **1:N** (One patient can have many appointments).
   * `Doctor` to `Appointment`: **1:N** (One doctor handles many appointments).
   * `Appointment` to `Prescription`: **1:N** (An appointment generates prescriptions).

---

### Task 2.2: E-commerce Platform

1. **Weak Entity & Justification:**
   * **Weak Entity:** `OrderItem`
   * **Justification:** An `OrderItem` cannot exist independently without a parent `Order`. Its existence depends entirely on `Order`, and its key is a composite discriminant (`OrderID` + `ItemNumber`).

2. **Many-to-Many (M:N) Relationship with Attributes:**
   * **Relationship:** `Order` ↔ `Product` (resolved via associative entity `OrderItem`).
   * **Attributes on Relationship:** `Quantity`, `UnitPriceAtOrderTime`.

---

## Part 4: Normalization Workshop

### Task 4.1: Denormalized Table Analysis

Given relation:
`StudentProject(StudentID, StudentName, StudentMajor, ProjectID, ProjectTitle, ProjectType, SupervisorID, SupervisorName, SupervisorDept, Role, HoursWorked, StartDate, EndDate)`

#### 1. Functional Dependencies (FDs):
* FD1: `StudentID` → `StudentName`, `StudentMajor`
* FD2: `ProjectID` → `ProjectTitle`, `ProjectType`, `SupervisorID`, `StartDate`, `EndDate`
* FD3: `SupervisorID` → `SupervisorName`, `SupervisorDept`
* FD4: {`StudentID`, `ProjectID`} → `Role`, `HoursWorked`

#### 2. Redundancy & Anomalies:
* **Update Anomaly:** Changing a supervisor’s department requires updating multiple rows for every project/student assigned to them.
* **Insertion Anomaly:** Cannot add a new student who hasn't been assigned to a project yet without inserting `NULL` values for project details.
* **Deletion Anomaly:** Deleting the only student on a project removes all records of that project and supervisor from the database.

#### 3. 1NF Analysis & 2NF Decomposition:
* **1NF:** Satisfied (all attributes are atomic).
* **Primary Key:** {`StudentID`, `ProjectID`}
* **Partial Dependencies (2NF Violations):** 
  * FD1 depends only on part of the key (`StudentID`).
  * FD2 depends only on part of the key (`ProjectID`).
* **2NF Decomposition:**
  * `Student(StudentID, StudentName, StudentMajor)`
  * `Project(ProjectID, ProjectTitle, ProjectType, SupervisorID, StartDate, EndDate)`
  * `Supervisor(SupervisorID, SupervisorName, SupervisorDept)`
  * `StudentProjectAssignment(StudentID, ProjectID, Role, HoursWorked)`

#### 4. 3NF Decomposition (Removing Transitive Dependencies):
* FD3 (`SupervisorID` → `SupervisorName`, `SupervisorDept`) was transitive inside `Project`.
* **Final 3NF Relations:**
  1. `Student` (**StudentID**, StudentName, StudentMajor)
  2. `Supervisor` (**SupervisorID**, SupervisorName, SupervisorDept)
  3. `Project` (**ProjectID**, ProjectTitle, ProjectType, *SupervisorID*, StartDate, EndDate)
  4. `ProjectAssignment` (**StudentID**, **ProjectID**, Role, HoursWorked)

---

### Task 4.2: Advanced Normalization

Given relation:
`CourseSchedule(StudentID, StudentMajor, CourseID, CourseName, InstructorID, InstructorName, TimeSlot, Room, Building)`

#### 1. Primary Key & Functional Dependencies:
* FD1: `StudentID` → `StudentMajor`
* FD2: `CourseID` → `CourseName`
* FD3: `InstructorID` → `InstructorName`
* FD4: {`TimeSlot`, `Room`} → `Building`
* FD5: {`CourseID`, `TimeSlot`} → `InstructorID`, `Room`
* FD6: {`StudentID`, `TimeSlot`} → `CourseID`
* **Primary Key:** {`StudentID`, `TimeSlot`}

#### 2. BCNF Decomposition:
The table violates BCNF because the LHS of FD1, FD2, FD3, FD4, FD5 are not superkeys.

**Decomposed BCNF Relations:**
1. `Student` (**StudentID**, StudentMajor)
2. `Course` (**CourseID**, CourseName)
3. `Instructor` (**InstructorID**, InstructorName)
4. `RoomLocation` (**TimeSlot**, **Room**, Building)
5. `ClassSection` (**CourseID**, **TimeSlot**, InstructorID, Room)
6. `StudentEnrollment` (**StudentID**, **TimeSlot**, *CourseID*)

#### 3. Information Loss Analysis:
* **Lossless Join:** Preserved. Joining tables reconstitutes the exact original relation without extra or missing tuples.
* **Dependency Preservation:** All functional dependencies are preserved across decomposed relations.

---

## Part 5: Design Challenge

### System: University Student Clubs & Organizations

#### 1. Normalized Relational Schema (3NF):

* `Club` (**ClubID**, ClubName, FoundedDate, Budget, *AdvisorID*)
* `FacultyAdvisor` (**AdvisorID**, Name, Email, Department)
* `Student` (**StudentID**, Name, Email, Major)
* `ClubMembership` (**StudentID**, **ClubID**, JoinDate)
* `ClubOfficer` (**OfficerID**, *StudentID*, *ClubID*, Position, TermYear)
* `Room` (**RoomID**, Building, RoomNumber, Capacity)
* `ClubEvent` (**EventID**, EventName, EventDate, *ClubID*, *RoomID*)
* `EventAttendance` (**StudentID**, **EventID**, AttendanceStatus)
* `ClubExpense` (**ExpenseID**, *ClubID*, Amount, Description, ExpenseDate)

#### 2. Design Decision & Justification:
* **Decision:** Separating `ClubMembership` and `ClubOfficer` into two distinct entities instead of placing `Position` as a nullable field in `ClubMembership`.
* **Justification:** A student can remain a general member for multiple years while holding different officer positions over time (e.g., Vice President in 2025, President in 2026). Keeping `ClubOfficer` separate prevents historical update anomalies and correctly models term limits.

#### 3. 3 Required Business Queries (English):
1. *"Find all students who are officers in the Computer Science Club."*
2. *"List all events scheduled for next week along with their reserved room numbers and host club names."*
3. *"Calculate total expenses and remaining budget for each club for the current academic year."*
