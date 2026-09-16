# ER Diagram Workshop – Submission Template

## Objective
To understand and apply ER modeling concepts by creating ER diagrams for real-world applications.

## Purpose
Gain hands-on experience in designing ER diagrams that represent database structure including entities, relationships, attributes, and constraints.

---

# Scenario A: City Fitness Club Management

**Business Context:**  
FlexiFit Gym wants a database to manage its members, trainers, and fitness programs.

**Requirements:**  
- Members register with name, membership type, and start date.  
- Each member can join multiple programs (Yoga, Zumba, Weight Training).  
- Trainers assigned to programs; a program may have multiple trainers.  
- Members may book personal training sessions with trainers.  
- Attendance recorded for each session.  
- Payments tracked for memberships and sessions.

### ER Diagram:
<img width="753" height="417" alt="image" src="https://github.com/user-attachments/assets/8e9bae53-2c7d-42a4-8baa-270d740be2ac" />


### Entities and Attributes

| Entity | Attributes (PK, FK) | Notes |
|--------|--------------------|-------|
| MEMBER     | MemberID (PK), Name, Gender, Phone               | Stores member details       |
| PROGRAM    | ProgramID (PK), ProgramName, ProgramType         | Stores fitness programs     |
| TRAINERS   | TrainerID (PK), TrainerName, PhoneNumber, Gender | Stores trainer details      |
| MEMBERSHIP | MembershipID (PK), MembershipType, Status, Price | Stores membership details   |
| PAYMENTS   | PaymentID (PK), PaymentType, Price               | Stores payment details      |

### Relationships and Constraints

| Relationship | Cardinality | Participation | Notes |
|--------------|------------|---------------|-------|
| MEMBER - joins — PROGRAM	        |M:N	|Partial	Members can join multiple programs     |
| PROGRAM - has — TRAINERS	        |M:N	|Partial	Programs can have multiple trainers    |
| MEMBER — has — MEMBERSHIP        	|1:1	|Total	Each member has one membership           |
| MEMBER — books — SESSIONS	        |1:N	|Partial	Members can book multiple sessions     |
| TRAINERS — provides — SESSIONS	  |1:N	|Partial	Trainers can provide multiple sessions |
| MEMBERSHIP — Paid for — PAYMENTS	|1:N	|Total	Membership payments are recorded         |

### Assumptions
- Each member can join one or more programs.
- Each program can have multiple trainers.
- Each session is associated with one member and one trainer.

---

# Scenario B: City Library Event & Book Lending System

**Business Context:**  
The Central Library wants to manage book lending and cultural events.

**Requirements:**  
- Members borrow books, with loan and return dates tracked.  
- Each book has title, author, and category.  
- Library organizes events; members can register.  
- Each event has one or more speakers/authors.  
- Rooms are booked for events and study.  
- Overdue fines apply for late returns.

### ER Diagram:


### Entities and Attributes

| Entity | Attributes (PK, FK) | Notes |
|--------|--------------------|-------|
| MEMBER           | MemberID (PK), Name, Gender, Phone                    | Stores member details               |
| BOOKS            | BookID (PK), Title, Author, Category                  | Stores book details                 |
| EVENTS           | EventID (PK), EventName, Price                        | Stores library events               |
| SPEAKERS/AUTHORS | Name, Role, Contact                                   | Stores speaker/author details       |
| ROOMS IN LIBRARY | PaymentID (PK), Price, PaymentType                    | Stores room booking payment details |
| STUDY PURPOSES   | SessionID (PK), SessionType, SessionDate, SessionTime | Stores study sessions               |
| FINES            | FineID (PK), Amount, PaidStatus                       | Stores overdue fine details         |


### Relationships and Constraints

| Relationship | Cardinality | Participation | Notes |
|--------------|------------|---------------|-------|
|              |            |               |       |
|              |            |               |       |
|              |            |               |       |

### Assumptions
- 
- 
- 

---

# Scenario C: Restaurant Table Reservation & Ordering

**Business Context:**  
A popular restaurant wants to manage reservations, orders, and billing.

**Requirements:**  
- Customers can reserve tables or walk in.  
- Each reservation includes date, time, and number of guests.  
- Customers place food orders linked to reservations.  
- Each order contains multiple dishes; dishes belong to categories (starter, main, dessert).  
- Bills generated per reservation, including food and service charges.  
- Waiters assigned to serve reservations.

### ER Diagram:
*Paste or attach your diagram here*  
![ER Diagram](er_diagram_restaurant.png)

### Entities and Attributes

| Entity | Attributes (PK, FK) | Notes |
|--------|--------------------|-------|
|        |                    |       |
|        |                    |       |
|        |                    |       |
|        |                    |       |
|        |                    |       |

### Relationships and Constraints

| Relationship | Cardinality | Participation | Notes |
|--------------|------------|---------------|-------|
|              |            |               |       |
|              |            |               |       |
|              |            |               |       |

### Assumptions
- 
- 
- 

---

## Instructions for Students

1. Complete **all three scenarios** (A, B, C).  
2. Identify entities, relationships, and attributes for each.  
3. Draw ER diagrams using **draw.io / diagrams.net** or hand-drawn & scanned.  
4. Fill in all tables and assumptions for each scenario.  
5. Export the completed Markdown (with diagrams) as **a single PDF**
