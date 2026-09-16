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
<img width="753" height="646" alt="image" src="https://github.com/user-attachments/assets/e105b710-7437-458c-9f9a-4dc845d705ad" />


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
| MEMBER — borrows — BOOKS             | M:N         | Partial       | Members can borrow multiple books              |
| MEMBER — register for — EVENTS       | M:N         | Partial       | Members can register for multiple events       |
| EVENTS — has — SPEAKERS/AUTHORS      | 1:N         | Total         | Each event has one or more speakers            |
| EVENTS — booked in — ROOMS           | N:1         | Total         | Events are held in library rooms               |
| ROOMS — booked for — STUDY PURPOSES  | 1:N         | Partial       | A room can be used for multiple study sessions |
| MEMBER — on late return pays — FINES | 1:N         | Partial       | A member may have multiple overdue fines       |



### Assumptions
- A member can borrow multiple books, but each book is borrowed by one member at a time.
- A member can register for multiple events
- Each event has at least one speaker or author.

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
<img width="752" height="546" alt="image" src="https://github.com/user-attachments/assets/aa05d68b-f4a4-46c7-b67c-d04e5d3e1b09" />


### Entities and Attributes

| Entity | Attributes (PK, FK) | Notes |
|--------|--------------------|-------|
| CUSTOMER    | CustomerID (PK), Name, Gender, Phone                          | Stores customer details    |
| RESERVATION | ReservationID (PK), Date, Time, NoOfGuests, ReservationType   | Stores reservation details |
| WAITER      | WaiterID (PK), Name, Phone, Email                             | Stores waiter details      |
| ORDERS      | OrderID (PK), OrderTime, Status                               | Stores order details       |
| ORDER_ITEM  | Quantity, UnitPrice                                           | Stores items in an order   |
| DISH        | DishID (PK), DishName, Price                                  | Stores dish details        |
| CATEGORY    | CategoryID (PK), CategoryName                                 | Stores dish categories     |
| BILL        | BillID (PK), BillDate, FoodAmount, ServiceAmount, TotalAmount | Stores billing details     |

### Relationships and Constraints

| Relationship | Cardinality | Participation | Notes |
|--------------|------------|---------------|-------|
| CUSTOMER — makes — RESERVATION     | 1:N         | Partial, Total | A customer can make multiple reservations |
| RESERVATION — assigned to — WAITER | N:1         | Total, Partial | A waiter can serve multiple reservations  |
| RESERVATION — booked in — ORDERS   | 1:N         | Partial, Total | A reservation can have multiple orders    |
| ORDERS — contains — ORDER_ITEM     | 1:N         | Total, Total   | Each order contains multiple items        |
| ORDER_ITEM — booked for — DISH     | N:1         | Total, Partial | Each order item refers to one dish        |
| DISH — belongs to — CATEGORY       | N:1         | Total, Total   | Each dish belongs to one category         |
| RESERVATION — generates — BILL     | 1:1         | Total, Total   | Each reservation generates one bill       |

### Assumptions
- A customer can make multiple reservations, but each reservation belongs to one customer.
- Each order can contain multiple dishes with their quantities.
- Each dish belongs to one category.

---

## Instructions for Students

1. Complete **all three scenarios** (A, B, C).  
2. Identify entities, relationships, and attributes for each.  
3. Draw ER diagrams using **draw.io / diagrams.net** or hand-drawn & scanned.  
4. Fill in all tables and assumptions for each scenario.  
5. Export the completed Markdown (with diagrams) as **a single PDF**
