# ✈️ Flight Booking and Management Digital Tool


**Author:** MURAHIRA MUHIRE Arsene  
**Student ID:** 27656  
**Institution:** Adventist University of Central Africa (AUCA)  



## 📋 Project Overview

A comprehensive Oracle PL/SQL database system designed to automate flight booking operations, manage seat allocation in real-time, implement dynamic pricing, and handle bookings efficiently. This project serves as the capstone for the Database Development with PL/SQL (INSY 8311) course.

**Author:** MURAHIRA MUHIRE Arsene  
**Student ID:** 27656  
**Institution:** Adventist University of Central Africa (AUCA)  
**Lecturer:** Eric Maniraguha  
**Academic Year:** 2025-2026, Semester I  
**Completion Date:** December 7, 2025




## PHASE I:🎯 Problem Statement

Many flight booking systems struggle with:
- Manual seat management
- Fixed pricing models
- Inefficient handling of cancellations and waitlists
- Lack of real-time availability tracking

Solution: An automated flight booking system that addresses these challenges through intelligent automation and data-driven decision making.
Target Users: Airlines, travel agencies, and passengers
BI Potential: Real-time analytics on booking patterns, revenue optimization, customer behavior analysis, and operational efficiency metrics.

---

## ✨ Key Features

### Core Functionality
- ✅ **Automated Seat Allocation** - Real-time seat tracking and availability management
- ✅ **Dynamic Pricing Engine** - Prices adjust based on demand and booking time
- ✅ **Smart Waitlist Management** - Automatic promotion when cancellations occur
- ✅ **Overbooking Protection** - Calculated safe limits based on historical no-show data
- ✅ **Multi-Payment Support** - Credit Card, Debit Card, PayPal, Bank Transfer, Mobile Money

### Technical Highlights
- 🔐 **Comprehensive Auditing** - All database operations logged
- 📊 **Business Intelligence** - Advanced analytics and reporting
- 🛡️ **Business Rule Enforcement** - Weekend-only data modifications
- 🔄 **Transaction Management** - ACID compliance with rollback capabilities
- 📈 **Window Functions** - Advanced analytical queries

---




## PHASE II: 🔄 Business Process Model 

Key Business Processes:

*Passenger Registration* → Create passenger profile with contact details.

*Flight Search & Selection* → Browse available flights by route/date.

*Seat Availability Check* → Real-time verification of seat capacity.

*Booking Creation* → Reserve seat and generate booking confirmation.

*Payment Processing* → Handle multiple payment methods securely.

*Booking Management* → Allow cancellations, modifications, check-ins.

*Waitlist Management* → Auto-promote when seats become available.

###### System Actors:

Passengers: Book flights, make payments, manage bookings.
Airline Staff: Manage flights, process refunds, handle special requests.
System: Automate seat allocation, pricing, waitlist promotion.

###### MIS Relevance:

Supports operational decisions (capacity planning).
Enables strategic decisions (pricing optimization).
Provides tactical insights (demand forecasting).



  ![BPMN](https://github.com/user-attachments/assets/4b5d41f9-6546-4e97-a6da-d6e6f16b4ad6)


    

## PHASE III: 🗂️ Logical Database Design 


### Entity Relationship Model

The system is designed with 5 core entities in 3rd Normal Form (3NF):


### Data Volume
- **Passengers:** 102 records
- **Flights:** 102 records
- **Bookings:** 102 records
- **Payments:** 102 records
- **Flight History:** 102 records



### Database Configuration
Database Name: wed_27656_arsene_flightBooking_db
Admin User: arsene
Tablespaces: DATA, INDEXES, TEMPORARY


<img width="596" height="885" alt="Picture1" src="https://github.com/user-attachments/assets/2efb71b3-b55d-47f4-bbdc-4e90273ec146" />


## Normalization:

*1NF*: All attributes atomic, no repeating groups.

*2NF*: All non-key attributes fully dependent on primary key.

*3NF*: No transitive dependencies.



## PHASE IV:  Pluggable Database creation
```sql
-- Run as SYSDBA
CREATE PLUGGABLE DATABASE WED_27656_arsene_FlightMS_DB
ADMIN USER admin_arsene IDENTIFIED BY arsene
ROLES = (DBA)
FILE_NAME_CONVERT = (
    'C:\\0RACLE21\\ORADATA\\ORCL2021\\PDBSEED\\',
    'C:\\0RACLE21\\ORADATA\\ORCL2021\\WED_27656_arsene_FlightMS_DB\\'
);

ALTER PLUGGABLE DATABASE WED_27656_arsene_FlightMS_DB OPEN;
ALTER PLUGGABLE DATABASE WED_27656_arsene_FlightMS_DB SAVE STATE;
ALTER SESSION SET CONTAINER = WED_27656_arsene_FlightMS_DB;


```
![PDB CREATION](https://github.com/user-attachments/assets/dd173ac7-b986-43b7-b585-c46a7bc368af)

##### Dashboard(EM)


<img width="932" height="413" alt="Dashboard1" src="https://github.com/user-attachments/assets/6fcd8b76-8f8f-491d-8bef-e99c6e0c2079" />



<img width="915" height="412" alt="Dashboard2" src="https://github.com/user-attachments/assets/d9e19c3e-c433-49b0-b954-b4754c73cd2c" />



---

## PHASE V: Table Implementation & Data Insertion

#### 3. Create Tablespaces
```sql
-- Create DATA tablespace
CREATE TABLESPACE flight_data
DATAFILE 'flight_data01.dbf' SIZE 500M
AUTOEXTEND ON NEXT 50M MAXSIZE 2G;

-- Create INDEX tablespace
CREATE TABLESPACE flight_indexes
DATAFILE 'flight_indexes01.dbf' SIZE 200M
AUTOEXTEND ON NEXT 20M MAXSIZE 1G;

-- Create TEMP tablespace
CREATE TEMPORARY TABLESPACE flight_temp
TEMPFILE 'flight_temp01.dbf' SIZE 200M
AUTOEXTEND ON NEXT 20M MAXSIZE 1G;
```


![TABLE SPACE](https://github.com/user-attachments/assets/3b4e4256-ffa6-49ca-8c3a-64905d8a20cd)

---

#### 4. Execute Database Scripts

**Create Tables:**
```sql
-- Connect to your PDB
CONN arsene/arsene@wed_27656_arsene_flightBooking_db

-- Create PASSENGERS table
CREATE TABLE PASSENGERS (
    PASSENGER_ID NUMBER PRIMARY KEY,
    FULL_NAME VARCHAR2(100) NOT NULL,
    EMAIL VARCHAR2(100) NOT NULL UNIQUE,
    PHONE VARCHAR2(20),
    PASSPORT_NUMBER VARCHAR2(20),
    LOYALTY_POINTS NUMBER DEFAULT 0,
    REGISTRATION_DATE DATE DEFAULT SYSDATE
);

-- Create FLIGHTS table
CREATE TABLE FLIGHTS (
    FLIGHT_ID NUMBER PRIMARY KEY,
    FLIGHT_NUMBER VARCHAR2(20) NOT NULL,
    DEPARTURE_CITY VARCHAR2(50) NOT NULL,
    ARRIVAL_CITY VARCHAR2(50) NOT NULL,
    DEPARTURE_TIME DATE NOT NULL,
    ARRIVAL_TIME DATE NOT NULL,
    SEATS_AVAILABLE NUMBER
);

-- Create BOOKINGS table
CREATE TABLE BOOKINGS (
    BOOKING_ID NUMBER PRIMARY KEY,
    PASSENGER_ID NUMBER NOT NULL,
    FLIGHT_ID NUMBER NOT NULL,
    BOOKING_DATE DATE DEFAULT SYSDATE,
    STATUS VARCHAR2(20) CHECK (STATUS IN ('Confirmed', 'Cancelled', 'Waitlisted', 'Checked-in', 'No-show')),
    CONSTRAINT FK_BOOKING_PASSENGER FOREIGN KEY (PASSENGER_ID) REFERENCES PASSENGERS(PASSENGER_ID),
    CONSTRAINT FK_BOOKING_FLIGHT FOREIGN KEY (FLIGHT_ID) REFERENCES FLIGHTS(FLIGHT_ID)
);

-- Create PAYMENTS table
CREATE TABLE PAYMENTS (
    BOOKING_ID NUMBER PRIMARY KEY,
    AMOUNT NUMBER(10,2) NOT NULL CHECK (AMOUNT > 0),
    PAYMENT_METHOD VARCHAR2(50) NOT NULL,
    CONSTRAINT FK_PAYMENT_BOOKING FOREIGN KEY (BOOKING_ID) REFERENCES BOOKINGS(BOOKING_ID)
);

-- Create FLIGHT_HISTORY table
CREATE TABLE FLIGHT_HISTORY (
    FLIGHT_ID NUMBER NOT NULL,
    PASSENGER_ID NUMBER NOT NULL,
    TRAVEL_DATE DATE NOT NULL,
    CONSTRAINT FK_HISTORY_FLIGHT FOREIGN KEY (FLIGHT_ID) REFERENCES FLIGHTS(FLIGHT_ID),
    CONSTRAINT FK_HISTORY_PASSENGER FOREIGN KEY (PASSENGER_ID) REFERENCES PASSENGERS(PASSENGER_ID)
);
```


![Tables Creation](https://github.com/user-attachments/assets/5d9efe7d-4b35-4ff5-b13f-796ce31796c5)

---
#### INSERTION OF DATA



![INSERTION OF DATAS](https://github.com/user-attachments/assets/11432f98-7b6d-4455-a67e-b1bbd32ba1bb)

---
#### ROWS INSERTED ON EACH TABLE

As guided, I inserted above 100 on each table

```sql
SELECT 'PASSENGERS' AS TABLE_NAME, COUNT(*) AS ROW_COUNT FROM PASSENGERS
UNION ALL
SELECT 'FLIGHTS' AS TABLE_NAME, COUNT(*) AS ROW_COUNT FROM FLIGHTS
UNION ALL
SELECT 'BOOKINGS' AS TABLE_NAME, COUNT(*) AS ROW_COUNT FROM BOOKINGS
UNION ALL
SELECT 'PAYMENTS' AS TABLE_NAME, COUNT(*) AS ROW_COUNT FROM PAYMENTS
UNION ALL
SELECT 'FLIGHT_HISTORY' AS TABLE_NAME, COUNT(*) AS ROW_COUNT FROM FLIGHT_HISTORY
ORDER BY TABLE_NAME;
```
![ROWS INSERTED ON EACH TABLEc](https://github.com/user-attachments/assets/4eacd938-1eef-4be4-9315-d869f5a04469)



####  Load Data into Tables

**Check Row Counts After Loading:**
```sql
SELECT * FROM PASSENGERS

```



![SELECT(PASSENGERS)](https://github.com/user-attachments/assets/4da1e7b9-7123-48e1-949a-f2da8bfb1c9c)

```sql
SELECT * FROM FLIGHTS;

```

![SELECT(FLIGHTS)](https://github.com/user-attachments/assets/fabd1f1d-a7b0-4cec-8188-34f2efbd813b)

```sql

SELECT * FROM BOOKINGS

```

![SELECT(BOOKINGS)](https://github.com/user-attachments/assets/443379fb-6334-442c-affe-26ab9d0e9656)


```sql

SELECT * FROM PAYMENTS

```




![SELECT(PAYMENTS)](https://github.com/user-attachments/assets/1c3c4062-476f-42f9-b0e1-ea30f5a4cc72)


```sql
 

SELECT * FROM FLIGHT_HISTORY


```



![SELECT(FLIGHT HISTORY)](https://github.com/user-attachments/assets/9eb2e527-acde-44fa-822b-f957ed476ee7)


### Check Foreign Key Relationships

**Verify all bookings have valid passengers:**
```sql
SELECT COUNT(*) AS orphaned_bookings
FROM BOOKINGS B
WHERE NOT EXISTS (
    SELECT 1 FROM PASSENGERS P 
    WHERE P.PASSENGER_ID = B.PASSENGER_ID
);
-- Expected: 0
```



![FK VERIFCATION](https://github.com/user-attachments/assets/b78f8c11-3451-46b0-895e-1e836ad13228)


---

### Check for Duplicate Primary Keys

```sql
-- Check for duplicate booking IDs
SELECT BOOKING_ID, COUNT(*) AS duplicate_count
FROM BOOKINGS
GROUP BY BOOKING_ID
HAVING COUNT(*) > 1;
-- Expected: No rows (all IDs are unique)

```
![Duplicate key](https://github.com/user-attachments/assets/90f87b86-0145-4dc5-b160-8cd37bc14096)




##### Finally completeness


![Data completeness](https://github.com/user-attachments/assets/d8e4ec97-1aa9-409d-b89a-7fc86102d150)




## 🔧 PHASE VI:  Database Interaction & Transaction 

### PART 1: CUSTOM EXCEPTIONS & ERROR LOG TABLE

```SQL
-- Create error log table for exception tracking
CREATE TABLE error_log (
    error_id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    error_date TIMESTAMP DEFAULT SYSTIMESTAMP,
    error_code NUMBER,
    error_message VARCHAR2(500),
    procedure_name VARCHAR2(100),
    user_name VARCHAR2(50) DEFAULT USER
);

```
![AUDIT LOG TABLE](https://github.com/user-attachments/assets/506c01b0-ee14-49fc-b894-74f8c38a5902)



### PART 2: PACKAGE SPECIFICATION (Public Interface)

```SQL
CREATE OR REPLACE PACKAGE flight_booking_pkg AS
    -- Custom Exceptions
    e_invalid_booking EXCEPTION;
    e_insufficient_seats EXCEPTION;
    e_invalid_payment EXCEPTION;
    e_passenger_not_found EXCEPTION;
    
    -- Procedure Declarations
    PROCEDURE create_booking(
        p_passenger_id IN NUMBER,
        p_flight_id IN NUMBER,
        p_booking_date IN DATE DEFAULT SYSDATE,
        p_booking_id OUT NUMBER,
        p_status OUT VARCHAR2
    );
    
    PROCEDURE update_booking_status(
        p_booking_id IN NUMBER,
        p_new_status IN VARCHAR2,
        p_success OUT BOOLEAN
    );
    
    PROCEDURE cancel_booking(
        p_booking_id IN NUMBER,
        p_refund_amount OUT NUMBER
    );
    
    PROCEDURE process_payment(
        p_booking_id IN NUMBER,
        p_amount IN NUMBER,
        p_payment_method IN VARCHAR2,
        p_payment_id OUT NUMBER
    );
    
    PROCEDURE bulk_update_status(
        p_old_status IN VARCHAR2,
        p_new_status IN VARCHAR2,
        p_updated_count OUT NUMBER
    );
    
    -- Function Declarations
    FUNCTION calculate_booking_revenue(
        p_booking_id IN NUMBER
    ) RETURN NUMBER;
    
    FUNCTION validate_passenger_email(
        p_email IN VARCHAR2
    ) RETURN BOOLEAN;
    
    FUNCTION get_flight_capacity(
        p_flight_id IN NUMBER
    ) RETURN NUMBER;
    
    FUNCTION calculate_total_revenue(
        p_start_date IN DATE DEFAULT NULL,
        p_end_date IN DATE DEFAULT NULL
    ) RETURN NUMBER;
    
    FUNCTION get_passenger_name(
        p_passenger_id IN NUMBER
    ) RETURN VARCHAR2;
    
    -- Cursor-based Procedure
    PROCEDURE generate_booking_report(
        p_status IN VARCHAR2 DEFAULT NULL
    );
    
END flight_booking_pkg;
/
```


![PACKAGE SPECIFICATION (Public Interface](https://github.com/user-attachments/assets/b2bf6fc1-f46b-485f-bfbc-f2c4ff760a62)

### PART 3: PACKAGE BODY (Implementation)
```SQL

CREATE OR REPLACE PACKAGE BODY flight_booking_pkg AS

    -- Private procedure for error logging
    PROCEDURE log_error(
        p_error_code IN NUMBER,
        p_error_msg IN VARCHAR2,
        p_proc_name IN VARCHAR2
    ) IS
        PRAGMA AUTONOMOUS_TRANSACTION;
    BEGIN
        INSERT INTO error_log (error_code, error_message, procedure_name)
        VALUES (p_error_code, p_error_msg, p_proc_name);
        COMMIT;
    END log_error;
```


    ```SQL
### PROCEDURE 1: Create Booking

    -- Purpose: Insert new booking with validation
    -- Parameters: IN (passenger_id, flight_id), OUT (booking_id, status)

    PROCEDURE create_booking(
        p_passenger_id IN NUMBER,
        p_flight_id IN NUMBER,
        p_booking_date IN DATE DEFAULT SYSDATE,
        p_booking_id OUT NUMBER,
        p_status OUT VARCHAR2
    ) IS
        v_passenger_exists NUMBER;
        v_flight_exists NUMBER;
        v_next_booking_id NUMBER;
    BEGIN
        -- Validate passenger exists
        SELECT COUNT(*) INTO v_passenger_exists
        FROM passengers
        WHERE passenger_id = p_passenger_id;
        
        IF v_passenger_exists = 0 THEN
            RAISE e_passenger_not_found;
        END IF;
        
        -- Validate flight exists
        SELECT COUNT(*) INTO v_flight_exists
        FROM flights
        WHERE flight_id = p_flight_id;
        
        IF v_flight_exists = 0 THEN
            RAISE_APPLICATION_ERROR(-20001, 'Flight does not exist');
        END IF;
        
        -- Get next booking ID
        SELECT NVL(MAX(booking_id), 0) + 1 INTO v_next_booking_id
        FROM bookings;
        
        -- Insert booking
        INSERT INTO bookings (booking_id, passenger_id, flight_id, booking_date, status)
        VALUES (v_next_booking_id, p_passenger_id, p_flight_id, p_booking_date, 'Confirmed');
        
        p_booking_id := v_next_booking_id;
        p_status := 'SUCCESS';
        
        COMMIT;
        
        DBMS_OUTPUT.PUT_LINE('Booking created successfully. Booking ID: ' || p_booking_id);
        
    EXCEPTION
        WHEN e_passenger_not_found THEN
            p_status := 'FAILED - Passenger not found';
            log_error(SQLCODE, 'Passenger ID ' || p_passenger_id || ' not found', 'create_booking');
            ROLLBACK;
        WHEN OTHERS THEN
            p_status := 'FAILED - ' || SQLERRM;
            log_error(SQLCODE, SQLERRM, 'create_booking');
            ROLLBACK;
    END create_booking;
    
    ```



 ### PROCEDURE 2: Update Booking Status
    -- Purpose: Change booking status with validation
    -- Parameters: IN (booking_id, new_status), OUT (success)
    
    ```SQL
    PROCEDURE update_booking_status(
        p_booking_id IN NUMBER,
        p_new_status IN VARCHAR2,
        p_success OUT BOOLEAN
    ) IS
        v_current_status VARCHAR2(20);
        v_booking_exists NUMBER;
    BEGIN
        -- Check if booking exists
        SELECT COUNT(*) INTO v_booking_exists
        FROM bookings
        WHERE booking_id = p_booking_id;
        
        IF v_booking_exists = 0 THEN
            RAISE e_invalid_booking;
        END IF;
        
        -- Get current status
        SELECT status INTO v_current_status
        FROM bookings
        WHERE booking_id = p_booking_id;
        
        -- Update status
        UPDATE bookings
        SET status = p_new_status
        WHERE booking_id = p_booking_id;
        
        p_success := TRUE;
        COMMIT;
        
        DBMS_OUTPUT.PUT_LINE('Booking ' || p_booking_id || ' status updated from ' || 
                            v_current_status || ' to ' || p_new_status);
        
    EXCEPTION
        WHEN e_invalid_booking THEN
            p_success := FALSE;
            log_error(-20002, 'Booking ID ' || p_booking_id || ' does not exist', 'update_booking_status');
            ROLLBACK;
        WHEN OTHERS THEN
            p_success := FALSE;
            log_error(SQLCODE, SQLERRM, 'update_booking_status');
            ROLLBACK;
    END update_booking_status;

    ```

 ### PROCEDURE 3: Cancel Booking
    -- Purpose: Cancel booking and calculate refund
    -- Parameters: IN (booking_id), OUT (refund_amount)
   
    ```sql
    
    PROCEDURE cancel_booking(
        p_booking_id IN NUMBER,
        p_refund_amount OUT NUMBER
    ) IS
        v_payment_amount NUMBER;
    BEGIN
        -- Get payment amount
        SELECT amount INTO v_payment_amount
        FROM payments
        WHERE booking_id = p_booking_id;
        
        -- Calculate refund (90% refund policy)
        p_refund_amount := v_payment_amount * 0.90;
        
        -- Update booking status
        UPDATE bookings
        SET status = 'Cancelled'
        WHERE booking_id = p_booking_id;
        
        COMMIT;
        
        DBMS_OUTPUT.PUT_LINE('Booking cancelled. Refund amount: $' || 
                            ROUND(p_refund_amount, 2));
        
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            p_refund_amount := 0;
            log_error(-20003, 'No payment found for booking ' || p_booking_id, 'cancel_booking');
            ROLLBACK;
        WHEN OTHERS THEN
            p_refund_amount := 0;
            log_error(SQLCODE, SQLERRM, 'cancel_booking');
            ROLLBACK;
    END cancel_booking;
    
    ```


### PROCEDURE 4: Process Payment
    -- Purpose: Record payment for booking
    -- Parameters: IN (booking_id, amount, method), OUT (payment_id)

```sql

    PROCEDURE process_payment(
        p_booking_id IN NUMBER,
        p_amount IN NUMBER,
        p_payment_method IN VARCHAR2,
        p_payment_id OUT NUMBER
    ) IS
        v_booking_exists NUMBER;
    BEGIN
        -- Validate amount
        IF p_amount <= 0 THEN
            RAISE e_invalid_payment;
        END IF;
        
        -- Check booking exists
        SELECT COUNT(*) INTO v_booking_exists
        FROM bookings
        WHERE booking_id = p_booking_id;
        
        IF v_booking_exists = 0 THEN
            RAISE e_invalid_booking;
        END IF;
        
        -- Insert payment (assuming PAYMENT_ID is auto-generated or managed)
        INSERT INTO payments (booking_id, amount, payment_method)
        VALUES (p_booking_id, p_amount, p_payment_method);
        
        p_payment_id := p_booking_id; -- Simplified for this example
        
        COMMIT;
        
        DBMS_OUTPUT.PUT_LINE('Payment processed: $' || p_amount || 
                            ' via ' || p_payment_method);
        
    EXCEPTION
        WHEN e_invalid_payment THEN
            p_payment_id := NULL;
            log_error(-20004, 'Invalid payment amount: ' || p_amount, 'process_payment');
            ROLLBACK;
        WHEN OTHERS THEN
            p_payment_id := NULL;
            log_error(SQLCODE, SQLERRM, 'process_payment');
            ROLLBACK;
    END process_payment;
```


### PROCEDURE 5: Bulk Update Status (Bulk Operations)


    -- Purpose: Update multiple bookings at once
    -- Parameters: IN (old_status, new_status), OUT (updated_count)
    
 ```sql

    PROCEDURE bulk_update_status(
        p_old_status IN VARCHAR2,
        p_new_status IN VARCHAR2,
        p_updated_count OUT NUMBER
    ) IS
        TYPE booking_id_tab IS TABLE OF bookings.booking_id%TYPE;
        v_booking_ids booking_id_tab;
    BEGIN
        -- Bulk collect booking IDs
        SELECT booking_id
        BULK COLLECT INTO v_booking_ids
        FROM bookings
        WHERE status = p_old_status;
        
        -- Bulk update
        FORALL i IN 1..v_booking_ids.COUNT
            UPDATE bookings
            SET status = p_new_status
            WHERE booking_id = v_booking_ids(i);
        
        p_updated_count := SQL%ROWCOUNT;
        
        COMMIT;
        
        DBMS_OUTPUT.PUT_LINE('Updated ' || p_updated_count || 
                            ' bookings from ' || p_old_status || 
                            ' to ' || p_new_status);
        
    EXCEPTION
        WHEN OTHERS THEN
            p_updated_count := 0;
            log_error(SQLCODE, SQLERRM, 'bulk_update_status');
            ROLLBACK;
    END bulk_update_status;
```


 ### FUNCTION 1: Calculate Booking Revenue
    -- Purpose: Get total payment for a booking
    -- Returns: Payment amount

```sql

    FUNCTION calculate_booking_revenue(
        p_booking_id IN NUMBER
    ) RETURN NUMBER IS
        v_revenue NUMBER := 0;
    BEGIN
        SELECT NVL(SUM(amount), 0)
        INTO v_revenue
        FROM payments
        WHERE booking_id = p_booking_id;
        
        RETURN v_revenue;
        
    EXCEPTION
        WHEN OTHERS THEN
            log_error(SQLCODE, SQLERRM, 'calculate_booking_revenue');
            RETURN 0;
    END calculate_booking_revenue;
```

### FUNCTION 2: Validate Passenger Email

    -- Purpose: Check if email format is valid
    -- Returns: TRUE/FALSE
    
  ```sql

    FUNCTION validate_passenger_email(
        p_email IN VARCHAR2
    ) RETURN BOOLEAN IS
    BEGIN
        -- Basic email validation (contains @ and .)
        IF p_email LIKE '%_@__%.__%' THEN
            RETURN TRUE;
        ELSE
            RETURN FALSE;
        END IF;
        
    EXCEPTION
        WHEN OTHERS THEN
            log_error(SQLCODE, SQLERRM, 'validate_passenger_email');
       RETURN FALSE;
    END validate_passenger_email;

```


 ### FUNCTION 3: Get Flight Capacity

 
    -- Purpose: Calculate available seats on flight
    -- Returns: Number of available seats

```sql

    FUNCTION get_flight_capacity(
        p_flight_id IN NUMBER
    ) RETURN NUMBER IS
        v_booked_seats NUMBER := 0;
        v_total_capacity NUMBER := 200; -- Assume 200 seats per flight
    BEGIN
        SELECT COUNT(*)
        INTO v_booked_seats
        FROM bookings
        WHERE flight_id = p_flight_id
        AND status IN ('Confirmed', 'Checked-in');
        
        RETURN v_total_capacity - v_booked_seats;
        
    EXCEPTION
        WHEN OTHERS THEN
            log_error(SQLCODE, SQLERRM, 'get_flight_capacity');
            RETURN 0;
    END get_flight_capacity;

```


### FUNCTION 4: Calculate Total Revenue
    -- Purpose: Calculate revenue for date range
    -- Returns: Total revenue
    
```sql

    FUNCTION calculate_total_revenue(
        p_start_date IN DATE DEFAULT NULL,
        p_end_date IN DATE DEFAULT NULL
    ) RETURN NUMBER IS
        v_total_revenue NUMBER := 0;
    BEGIN
        IF p_start_date IS NULL AND p_end_date IS NULL THEN
            SELECT NVL(SUM(amount), 0)
            INTO v_total_revenue
            FROM payments;
        ELSE
            SELECT NVL(SUM(p.amount), 0)
            INTO v_total_revenue
            FROM payments p
            JOIN bookings b ON p.booking_id = b.booking_id
            WHERE b.booking_date BETWEEN 
                  NVL(p_start_date, b.booking_date) AND 
                  NVL(p_end_date, b.booking_date);
        END IF;
        
        RETURN v_total_revenue;
        
    EXCEPTION
        WHEN OTHERS THEN
            log_error(SQLCODE, SQLERRM, 'calculate_total_revenue');
            RETURN 0;
    END calculate_total_revenue;

```

### FUNCTION 5: Get Passenger Name (Lookup)
    -- Purpose: Retrieve passenger full name
    -- Returns: Full name

 ```sql

    FUNCTION get_passenger_name(
        p_passenger_id IN NUMBER
    ) RETURN VARCHAR2 IS
        v_full_name VARCHAR2(200);
    BEGIN
        SELECT first_name || ' ' || last_name
        INTO v_full_name
        FROM passengers
        WHERE passenger_id = p_passenger_id;
        
        RETURN v_full_name;
        
    EXCEPTION
        WHEN NO_DATA_FOUND THEN
            RETURN 'Unknown Passenger';
        WHEN OTHERS THEN
            log_error(SQLCODE, SQLERRM, 'get_passenger_name');
            RETURN 'Error';
    END get_passenger_name;

```


### PROCEDURE 6: Generate Booking Report (Cursor)
    -- Purpose: Display booking details using explicit cursor

   ```sql

    PROCEDURE generate_booking_report(
        p_status IN VARCHAR2 DEFAULT NULL
    ) IS
        -- Explicit cursor
        CURSOR c_bookings IS
            SELECT b.booking_id, b.booking_date, b.status,
                   p.first_name || ' ' || p.last_name AS passenger_name,
                   f.flight_number, f.origin, f.destination,
                   pay.amount
            FROM bookings b
            JOIN passengers p ON b.passenger_id = p.passenger_id
            JOIN flights f ON b.flight_id = f.flight_id
            LEFT JOIN payments pay ON b.booking_id = pay.booking_id
            WHERE b.status = NVL(p_status, b.status)
            ORDER BY b.booking_date DESC;
        
        v_booking c_bookings%ROWTYPE;
        v_count NUMBER := 0;
    BEGIN
        DBMS_OUTPUT.PUT_LINE('===== BOOKING REPORT =====');
        DBMS_OUTPUT.PUT_LINE('Status Filter: ' || NVL(p_status, 'ALL'));
        DBMS_OUTPUT.PUT_LINE('--------------------------');
        
        -- Open cursor
        OPEN c_bookings;
        
        -- Fetch rows
        LOOP
            FETCH c_bookings INTO v_booking;
            EXIT WHEN c_bookings%NOTFOUND;
            
            v_count := v_count + 1;
            
            DBMS_OUTPUT.PUT_LINE('Booking ID: ' || v_booking.booking_id);
            DBMS_OUTPUT.PUT_LINE('Passenger: ' || v_booking.passenger_name);
            DBMS_OUTPUT.PUT_LINE('Flight: ' || v_booking.flight_number || 
                                ' (' || v_booking.origin || ' → ' || 
                                v_booking.destination || ')');
            DBMS_OUTPUT.PUT_LINE('Amount: $' || NVL(v_booking.amount, 0));
            DBMS_OUTPUT.PUT_LINE('Status: ' || v_booking.status);
            DBMS_OUTPUT.PUT_LINE('--------------------------');
        END LOOP;
        
        -- Close cursor
        CLOSE c_bookings;
        
        DBMS_OUTPUT.PUT_LINE('Total records: ' || v_count);
        
    EXCEPTION
        WHEN OTHERS THEN
            IF c_bookings%ISOPEN THEN
                CLOSE c_bookings;
            END IF;
            log_error(SQLCODE, SQLERRM, 'generate_booking_report');
    END generate_booking_report;

END flight_booking_pkg;
/

```
![PACKAGE BODY (Implementation)](https://github.com/user-attachments/assets/10d5e77c-41c3-4a6f-8763-54ad397a7dbc)




### PART 4: WINDOW FUNCTIONS QUERIES

#### Window Function 1: Rank passengers by spending


```sql

SELECT 
    p.passenger_id,
    p.first_name || ' ' || p.last_name AS passenger_name,
    SUM(pay.amount) AS total_spent,
    ROW_NUMBER() OVER (ORDER BY SUM(pay.amount) DESC) AS row_num,
    RANK() OVER (ORDER BY SUM(pay.amount) DESC) AS rank,
    DENSE_RANK() OVER (ORDER BY SUM(pay.amount) DESC) AS dense_rank
FROM passengers p
JOIN bookings b ON p.passenger_id = b.passenger_id
JOIN payments pay ON b.booking_id = pay.booking_id
GROUP BY p.passenger_id, p.first_name, p.last_name;

```


![Window Function 1 (Rank passengers by spending)](https://github.com/user-attachments/assets/44828851-ea28-4885-a24e-cd22cec8b2c7)

### Window Function 2: Payment trends with LAG/LEAD


```sql

SELECT 
    booking_id,
    amount,
    payment_method,
    LAG(amount, 1) OVER (ORDER BY booking_id) AS previous_payment,
    LEAD(amount, 1) OVER (ORDER BY booking_id) AS next_payment,
    amount - LAG(amount, 1) OVER (ORDER BY booking_id) AS payment_change
FROM payments;

```

![Window Function 2  Payment trends with LAG LEAD](https://github.com/user-attachments/assets/454325c7-437a-4f5f-8287-5109c709d281)


### Window Function 3: Bookings per flight with running total
```sql
SELECT 
    f.flight_id,
    f.flight_number,
    f.origin || ' → ' || f.destination AS route,
    COUNT(b.booking_id) AS total_bookings,
    SUM(COUNT(b.booking_id)) OVER (ORDER BY f.flight_id) AS running_total,
    ROUND(
        RATIO_TO_REPORT(COUNT(b.booking_id)) OVER () * 100, 2
    ) AS percentage_of_total
FROM flights f
LEFT JOIN bookings b ON f.flight_id = b.flight_id
GROUP BY f.flight_id, f.flight_number, f.origin, f.destination;

```

![Window Function 3  (Bookings per flight with running total)](https://github.com/user-attachments/assets/4b7a5231-3023-4cbd-9483-567f8bb19dda)


### Window Function 4: Revenue by payment method with partitioning
```sql
SELECT 
    payment_method,
    booking_id,
    amount,
    SUM(amount) OVER (PARTITION BY payment_method) AS method_total,
    AVG(amount) OVER (PARTITION BY payment_method) AS method_average,
    ROW_NUMBER() OVER (PARTITION BY payment_method ORDER BY amount DESC) AS rank_in_method
FROM payments;
```


![Window Function 4 Revenue by payment method with partitioning](https://github.com/user-attachments/assets/92b2eeed-676d-4d58-bd89-1d1fb9ab99a0)


 #### PART 5: TESTING  AND CALLING  PROCEDURES AND FUNCTION
-- Enable output
```sql

SET SERVEROUTPUT ON;
```

#### CALLiNG PROCEDURE 1: Create Booking

```sql
DECLARE
    v_booking_id NUMBER;
    v_status VARCHAR2(100);
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== TEST 1: Create Booking ===');
    flight_booking_pkg.create_booking(
        p_passenger_id => 586,
        p_flight_id => 1,
        p_booking_id => v_booking_id,
        p_status => v_status
    );
    DBMS_OUTPUT.PUT_LINE('Result: ' || v_status);
    DBMS_OUTPUT.PUT_LINE('');
END;
/
```


![TEST 1 (Create Booking)](https://github.com/user-attachments/assets/6973ffe7-2d26-4eeb-8f90-82fef74763d2)

#### CALLING PROCEDURE 2: Update Booking Status

```sql

DECLARE
    v_success BOOLEAN;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== TEST 2: Update Booking Status ===');
    flight_booking_pkg.update_booking_status(
        p_booking_id => 1,
        p_new_status => 'Checked-in',
        p_success => v_success
    );
    IF v_success THEN
        DBMS_OUTPUT.PUT_LINE('Status update: SUCCESS');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Status update: FAILED');
    END IF;
    DBMS_OUTPUT.PUT_LINE('');
END;
/
```

![TEST 2 Update Booking Status](https://github.com/user-attachments/assets/c1e5219d-57ec-4a9b-9c0a-6596bbb28cf9)

#### CALL FUNCTION 2: Calculate Revenue Function

```sql
DECLARE
    v_revenue NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== TEST 3: Calculate Revenue ===');
    v_revenue := flight_booking_pkg.calculate_booking_revenue(1);
    DBMS_OUTPUT.PUT_LINE('Booking 1 Revenue: $' || v_revenue);
    DBMS_OUTPUT.PUT_LINE('');
END;
/
```

![TEST 3 Calculate Revenue Function](https://github.com/user-attachments/assets/7c56c24a-2a2b-4625-ac7a-26ce4939a004)


  
#### CAL: Validate Email Function

```sql

DECLARE
    v_valid BOOLEAN;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== TEST 4: Email Validation ===');
    v_valid := flight_booking_pkg.validate_passenger_email('test@example.com');
    IF v_valid THEN
        DBMS_OUTPUT.PUT_LINE('Email valid: TRUE');
    ELSE
        DBMS_OUTPUT.PUT_LINE('Email valid: FALSE');
    END IF;
    DBMS_OUTPUT.PUT_LINE('');
END;
/
```

  ![TEST 4 (Validate Email Function)](https://github.com/user-attachments/assets/7e2f26e6-4453-4b6e-a4cc-409d53248544)

  
#### CALLING FUNCTION 5: Get Flight Capacity
```sql
DECLARE
    v_capacity NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== TEST 5: Flight Capacity ===');
    v_capacity := flight_booking_pkg.get_flight_capacity(1);
    DBMS_OUTPUT.PUT_LINE('Available seats on Flight 1: ' || v_capacity);
    DBMS_OUTPUT.PUT_LINE('');
END;
/
```

  ![TEST 5 Get Flight Capacity](https://github.com/user-attachments/assets/da53375a-7de7-4abc-96af-8a6dfbb656d6)


#### TEST  6: Generate Report (Cursor)
```sql
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== TEST 6: Booking Report ===');
    flight_booking_pkg.generate_booking_report('Confirmed');
END;
/
```

![TEST 6 Generate Report (Cursor)](https://github.com/user-attachments/assets/40a4755a-569f-4abc-9a7b-d5088baae2f9)


#### CALL 7: Calculate Total Revenue

```sql

DECLARE
    v_total NUMBER;
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== TEST 7: Total Revenue ===');
    v_total := flight_booking_pkg.calculate_total_revenue();
    DBMS_OUTPUT.PUT_LINE('Total Revenue: $' || ROUND(v_total, 2));
    DBMS_OUTPUT.PUT_LINE('');
END;
/

```

![TEST 7 Calculate Total Revenue](https://github.com/user-attachments/assets/5513cbe1-4676-478d-a83a-2da69d05d3d9)


#### CALL 8: Get Passenger Name

```sql
DECLARE
    v_name VARCHAR2(200);
BEGIN
    DBMS_OUTPUT.PUT_LINE('=== TEST 8: Get Passenger Name ===');
    v_name := flight_booking_pkg.get_passenger_name(586);
    DBMS_OUTPUT.PUT_LINE('Passenger Name: ' || v_name);
    DBMS_OUTPUT.PUT_LINE('');
END;
/
```


![TEST 8 Get Passenger Name](https://github.com/user-attachments/assets/1aaafad7-9159-47b8-9b78-d6af5d929a2b)


## PHASE VIII: Advanced Programming & Auditing


```sql

CREATE TABLE public_holidays (
    holiday_id NUMBER PRIMARY KEY,
    holiday_name VARCHAR2(100) NOT NULL,
    holiday_date DATE NOT NULL UNIQUE,
    description VARCHAR2(200),
    created_date DATE DEFAULT SYSDATE
);

```
![PUBLIC HOLIDAYS TABLE CREATED](https://github.com/user-attachments/assets/32f4a91c-d5fe-4398-9b4e-0e049f19c10a)


#### Inserting  Sample Holidays:

```sql

-- Insert upcoming public holidays (December 2025 - January 2026)
INSERT INTO public_holidays (holiday_id, holiday_name, holiday_date, description)
VALUES (seq_holiday_id.NEXTVAL, 'Christmas Day', TO_DATE('2025-12-25','YYYY-MM-DD'), 'Christmas Holiday');

INSERT INTO public_holidays (holiday_id, holiday_name, holiday_date, description)
VALUES (seq_holiday_id.NEXTVAL, 'Boxing Day', TO_DATE('2025-12-26','YYYY-MM-DD'), 'Day after Christmas');

INSERT INTO public_holidays (holiday_id, holiday_name, holiday_date, description)
VALUES (seq_holiday_id.NEXTVAL, 'New Year Day', TO_DATE('2026-01-01','YYYY-MM-DD'), 'New Year Celebration');

INSERT INTO public_holidays (holiday_id, holiday_name, holiday_date, description)
VALUES (seq_holiday_id.NEXTVAL, 'Liberation Day', TO_DATE('2026-01-07','YYYY-MM-DD'), 'Rwanda Liberation Day');

COMMIT;

```

![INSERTION OF PUBLIC HOLIDAYS](https://github.com/user-attachments/assets/5acd84ad-4f9e-466c-8e08-67dcd3bf51e1)


#### STEP 2: AUDIT LOG TABLE
```sql

CREATE TABLE audit_log (
    audit_id NUMBER PRIMARY KEY,
    table_name VARCHAR2(50) NOT NULL,
    operation_type VARCHAR2(10) NOT NULL, -- INSERT, UPDATE, DELETE
    operation_date DATE DEFAULT SYSDATE,
    operation_day VARCHAR2(15), -- Day of week
    username VARCHAR2(50),
    attempt_status VARCHAR2(20), -- ALLOWED, DENIED
    denial_reason VARCHAR2(200),
    old_values CLOB,
    new_values CLOB,
    ip_address VARCHAR2(50),
    session_id NUMBER
);

```

![AUDIT TABLE CREATED](https://github.com/user-attachments/assets/f6aa55ce-7968-4ef2-82b5-54a89b33e072)



-- STEP 3: AUDIT LOGGING FUNCTION

```sql
CREATE OR REPLACE FUNCTION log_audit_entry(
    p_table_name VARCHAR2,
    p_operation_type VARCHAR2,
    p_attempt_status VARCHAR2,
    p_denial_reason VARCHAR2 DEFAULT NULL,
    p_old_values CLOB DEFAULT NULL,
    p_new_values CLOB DEFAULT NULL
) RETURN NUMBER
IS
    PRAGMA AUTONOMOUS_TRANSACTION;
    v_audit_id NUMBER;
    v_username VARCHAR2(50);
    v_day_name VARCHAR2(15);
    v_ip_address VARCHAR2(50);
    v_session_id NUMBER;
BEGIN
    -- Get current user information
    SELECT USER INTO v_username FROM DUAL;
    
    -- Get day name
    SELECT TO_CHAR(SYSDATE, 'DAY') INTO v_day_name FROM DUAL;
    
    -- Get session information
    SELECT SYS_CONTEXT('USERENV', 'IP_ADDRESS'),
           SYS_CONTEXT('USERENV', 'SESSIONID')
    INTO v_ip_address, v_session_id
    FROM DUAL;
```


![FUNCTION LOG_AUDIT_ENTRY COMPILED](https://github.com/user-attachments/assets/d210f0be-da84-4994-a34a-2df7f30bbd16)


##### Generate new audit ID

```SQL

    v_audit_id := seq_audit_id.NEXTVAL;
    
    -- Insert audit log entry
    INSERT INTO audit_log (
        audit_id,
        table_name,
        operation_type,
        operation_date,
        operation_day,
        username,
        attempt_status,
        denial_reason,
        old_values,
        new_values,
        ip_address,
        session_id
    ) VALUES (
        v_audit_id,
        p_table_name,
        p_operation_type,
        SYSDATE,
        TRIM(v_day_name),
        v_username,
        p_attempt_status,
        p_denial_reason,
        p_old_values,
        p_new_values,
        v_ip_address,
        v_session_id
    );
    
    COMMIT;
    RETURN v_audit_id;
    
EXCEPTION
    WHEN OTHERS THEN
        ROLLBACK;
        RETURN -1;
END log_audit_entry;
/

```




##### STEP 4: RESTRICTION CHECK FUNCTION
```SQL

CREATE OR REPLACE FUNCTION check_operation_allowed(
    p_operation_type VARCHAR2,
    p_table_name VARCHAR2
) RETURN VARCHAR2
IS
    v_day_name VARCHAR2(15);
    v_day_number NUMBER;
    v_current_date DATE := TRUNC(SYSDATE);
    v_is_holiday NUMBER;
    v_denial_reason VARCHAR2(200);
BEGIN
    -- Get current day information
    SELECT TO_CHAR(SYSDATE, 'DAY'), TO_CHAR(SYSDATE, 'D')
    INTO v_day_name, v_day_number
    FROM DUAL;
    
    v_day_name := TRIM(v_day_name);
    
    -- Check if today is a public holiday
    SELECT COUNT(*) INTO v_is_holiday
    FROM public_holidays
    WHERE TRUNC(holiday_date) = v_current_date;
    
    -- RULE 1: Block operations on public holidays
    IF v_is_holiday > 0 THEN
        SELECT 'DENIED: Operation not allowed on PUBLIC HOLIDAY (' || holiday_name || ')'
        INTO v_denial_reason
        FROM public_holidays
        WHERE TRUNC(holiday_date) = v_current_date;
        
        RETURN v_denial_reason;
    END IF;
    
    -- RULE 2: Block operations on weekdays (Monday=2 to Friday=6)
    -- Oracle: 1=Sunday, 2=Monday, 3=Tuesday, 4=Wednesday, 5=Thursday, 6=Friday, 7=Saturday
    IF v_day_number BETWEEN 2 AND 6 THEN
        v_denial_reason := 'DENIED: Operation not allowed on WEEKDAY (' || v_day_name || '). Only weekends allowed.';
        RETURN v_denial_reason;
    END IF;
    
    -- RULE 3: Allow operations on weekends (Saturday=7, Sunday=1)
    IF v_day_number IN (1, 7) THEN
        RETURN 'ALLOWED';
    END IF;
    
    -- Default denial
    RETURN 'DENIED: Unknown day condition';
    
EXCEPTION
    WHEN OTHERS THEN
        RETURN 'ERROR: ' || SQLERRM;
END check_operation_allowed;
/

```
![FUNCTION CHECK_OPERATION_ALLOWED](https://github.com/user-attachments/assets/08293043-2df2-47ec-9732-2e5816032674)


#### STEP 5: SIMPLE TRIGGERS FOR EACH TABLE



##### TRIGGER 1: PASSENGERS TABLE RESTRICTION

```SQL

CREATE OR REPLACE TRIGGER trg_passengers_restrict
BEFORE INSERT OR UPDATE OR DELETE ON passengers
FOR EACH ROW
DECLARE
    v_operation VARCHAR2(10);
    v_check_result VARCHAR2(200);
    v_old_values CLOB;
    v_new_values CLOB;
    v_audit_id NUMBER;
BEGIN
    -- Determine operation type
    IF INSERTING THEN
        v_operation := 'INSERT';
        v_new_values := 'ID: ' || :NEW.passenger_id || ', Name: ' || :NEW.full_name || 
                       ', Email: ' || :NEW.email;
    ELSIF UPDATING THEN
        v_operation := 'UPDATE';
        v_old_values := 'ID: ' || :OLD.passenger_id || ', Name: ' || :OLD.full_name;
        v_new_values := 'ID: ' || :NEW.passenger_id || ', Name: ' || :NEW.full_name;
    ELSIF DELETING THEN
        v_operation := 'DELETE';
        v_old_values := 'ID: ' || :OLD.passenger_id || ', Name: ' || :OLD.full_name;
    END IF;
    
    -- Check if operation is allowed
    v_check_result := check_operation_allowed(v_operation, 'PASSENGERS');
    
    -- If operation is denied, log and raise error
    IF v_check_result != 'ALLOWED' THEN
        -- Log the denied attempt
        v_audit_id := log_audit_entry(
            p_table_name => 'PASSENGERS',
            p_operation_type => v_operation,
            p_attempt_status => 'DENIED',
            p_denial_reason => v_check_result,
            p_old_values => v_old_values,
            p_new_values => v_new_values
        );
        
        -- Raise application error
        RAISE_APPLICATION_ERROR(-20001, v_check_result);
    ELSE
        -- Log the allowed attempt
        v_audit_id := log_audit_entry(
            p_table_name => 'PASSENGERS',
            p_operation_type => v_operation,
            p_attempt_status => 'ALLOWED',
            p_denial_reason => NULL,
            p_old_values => v_old_values,
            p_new_values => v_new_values
        );
    END IF;
END;
/
```
 


![TRIGGER PASSENGERS_RESTRICT](https://github.com/user-attachments/assets/78d5473a-27f5-4eee-adbc-2e6fc2007ad7)


#####    TRIGGER 2: FLIGHTS TABLE RESTRICTION

```SQL
CREATE OR REPLACE TRIGGER trg_flights_restrict
BEFORE INSERT OR UPDATE OR DELETE ON flights
FOR EACH ROW
DECLARE
    v_operation VARCHAR2(10);
    v_check_result VARCHAR2(200);
    v_old_values CLOB;
    v_new_values CLOB;
    v_audit_id NUMBER;
BEGIN
    -- Determine operation type
    IF INSERTING THEN
        v_operation := 'INSERT';
        v_new_values := 'Flight: ' || :NEW.flight_number || ', Route: ' || 
                       :NEW.departure_city || ' to ' || :NEW.arrival_city;
    ELSIF UPDATING THEN
        v_operation := 'UPDATE';
        v_old_values := 'Flight: ' || :OLD.flight_number;
        v_new_values := 'Flight: ' || :NEW.flight_number;
    ELSIF DELETING THEN
        v_operation := 'DELETE';
        v_old_values := 'Flight: ' || :OLD.flight_number;
    END IF;
    
    -- Check if operation is allowed
    v_check_result := check_operation_allowed(v_operation, 'FLIGHTS');
    
    -- If operation is denied, log and raise error
    IF v_check_result != 'ALLOWED' THEN
        v_audit_id := log_audit_entry(
            p_table_name => 'FLIGHTS',
            p_operation_type => v_operation,
            p_attempt_status => 'DENIED',
            p_denial_reason => v_check_result,
            p_old_values => v_old_values,
            p_new_values => v_new_values
        );
        
        RAISE_APPLICATION_ERROR(-20002, v_check_result);
    ELSE
        v_audit_id := log_audit_entry(
            p_table_name => 'FLIGHTS',
            p_operation_type => v_operation,
            p_attempt_status => 'ALLOWED',
            p_denial_reason => NULL,
            p_old_values => v_old_values,
            p_new_values => v_new_values
        );
    END IF;
END;
/
```
![TRIGGER FLIGHTS RESTRICT](https://github.com/user-attachments/assets/d736b008-d317-49d8-b6a6-1e5d202c2b2f)


 #### TRIGGER 3: BOOKINGS TABLE RESTRICTION
 
```SQL
CREATE OR REPLACE TRIGGER trg_bookings_restrict
BEFORE INSERT OR UPDATE OR DELETE ON bookings
FOR EACH ROW
DECLARE
    v_operation VARCHAR2(10);
    v_check_result VARCHAR2(200);
    v_old_values CLOB;
    v_new_values CLOB;
    v_audit_id NUMBER;
BEGIN
    IF INSERTING THEN
        v_operation := 'INSERT';
        v_new_values := 'Booking ID: ' || :NEW.booking_id || ', Passenger: ' || :NEW.passenger_id;
    ELSIF UPDATING THEN
        v_operation := 'UPDATE';
        v_old_values := 'Booking ID: ' || :OLD.booking_id;
        v_new_values := 'Booking ID: ' || :NEW.booking_id;
    ELSIF DELETING THEN
        v_operation := 'DELETE';
        v_old_values := 'Booking ID: ' || :OLD.booking_id;
    END IF;
    
    v_check_result := check_operation_allowed(v_operation, 'BOOKINGS');
    
    IF v_check_result != 'ALLOWED' THEN
        v_audit_id := log_audit_entry(
            p_table_name => 'BOOKINGS',
            p_operation_type => v_operation,
            p_attempt_status => 'DENIED',
            p_denial_reason => v_check_result,
            p_old_values => v_old_values,
            p_new_values => v_new_values
        );
        
        RAISE_APPLICATION_ERROR(-20003, v_check_result);
    ELSE
        v_audit_id := log_audit_entry(
            p_table_name => 'BOOKINGS',
            p_operation_type => v_operation,
            p_attempt_status => 'ALLOWED',
            p_denial_reason => NULL,
            p_old_values => v_old_values,
            p_new_values => v_new_values
        );
    END IF;
END;
/
```


![TRIGGER BOOKING RESTRICT](https://github.com/user-attachments/assets/dd87050c-97e7-4abb-8e0f-87513528808b)



#### STEP 6: COMPOUND TRIGGER (Advanced Example for PAYMENTS)

```SQL

CREATE OR REPLACE TRIGGER trg_payments_compound
FOR INSERT OR UPDATE OR DELETE ON payments
COMPOUND TRIGGER
    
    -- Collection to store operation details
    TYPE t_operation_rec IS RECORD (
        operation_type VARCHAR2(10),
        payment_id NUMBER,
        booking_id NUMBER,
        amount NUMBER,
        payment_method VARCHAR2(30)
    );
    
    TYPE t_operations IS TABLE OF t_operation_rec INDEX BY PLS_INTEGER;
    v_operations t_operations;
    v_counter PLS_INTEGER := 0;
    
    -- BEFORE STATEMENT
    BEFORE STATEMENT IS
    BEGIN
        v_counter := 0;
        DBMS_OUTPUT.PUT_LINE('=== COMPOUND TRIGGER: Starting Payment Operation ===');
    END BEFORE STATEMENT;
    
    -- BEFORE EACH ROW
    BEFORE EACH ROW IS
        v_check_result VARCHAR2(200);
        v_operation VARCHAR2(10);
        v_old_values CLOB;
        v_new_values CLOB;
        v_audit_id NUMBER;
    BEGIN
        -- Determine operation
        IF INSERTING THEN
            v_operation := 'INSERT';
            v_new_values := 'Payment for Booking: ' || :NEW.booking_id || ', Amount: ' || :NEW.amount;
        ELSIF UPDATING THEN
            v_operation := 'UPDATE';
            v_old_values := 'Payment ID: ' || :OLD.payment_id || ', Amount: ' || :OLD.amount;
            v_new_values := 'Payment ID: ' || :NEW.payment_id || ', Amount: ' || :NEW.amount;
        ELSIF DELETING THEN
            v_operation := 'DELETE';
            v_old_values := 'Payment ID: ' || :OLD.payment_id;
        END IF;
        
        -- Check restriction
        v_check_result := check_operation_allowed(v_operation, 'PAYMENTS');
        
        IF v_check_result != 'ALLOWED' THEN
            v_audit_id := log_audit_entry(
                p_table_name => 'PAYMENTS',
                p_operation_type => v_operation,
                p_attempt_status => 'DENIED',
                p_denial_reason => v_check_result,
                p_old_values => v_old_values,
                p_new_values => v_new_values
            );
            
            RAISE_APPLICATION_ERROR(-20004, v_check_result);
        ELSE
            -- Store operation details
            v_counter := v_counter + 1;
            v_operations(v_counter).operation_type := v_operation;
            
            IF INSERTING OR UPDATING THEN
                v_operations(v_counter).payment_id := :NEW.payment_id;
                v_operations(v_counter).booking_id := :NEW.booking_id;
                v_operations(v_counter).amount := :NEW.amount;
                v_operations(v_counter).payment_method := :NEW.payment_method;
            ELSIF DELETING THEN
                v_operations(v_counter).payment_id := :OLD.payment_id;
                v_operations(v_counter).booking_id := :OLD.booking_id;
                v_operations(v_counter).amount := :OLD.amount;
            END IF;
            
            v_audit_id := log_audit_entry(
                p_table_name => 'PAYMENTS',
                p_operation_type => v_operation,
                p_attempt_status => 'ALLOWED',
                p_denial_reason => NULL,
                p_old_values => v_old_values,
                p_new_values => v_new_values
            );
        END IF;
    END BEFORE EACH ROW;
    
    -- AFTER STATEMENT
    AFTER STATEMENT IS
    BEGIN
        DBMS_OUTPUT.PUT_LINE('Total operations processed: ' || v_counter);
        DBMS_OUTPUT.PUT_LINE('=== COMPOUND TRIGGER: Payment Operation Complete ===');
        v_operations.DELETE;
    END AFTER STATEMENT;
    
END trg_payments_compound;
/

```

![COMPOUND TRIGGER ON PAYMENTS](https://github.com/user-attachments/assets/5643b946-9de4-4f71-93e2-5421548566da)


#### TESTING SECTION

SET SERVEROUTPUT ON;

##### PROMPT TESTING: Business Rule Restrictions and Auditing

#### Test Query 1: Check current day
```SQL

SELECT 
    TO_CHAR(SYSDATE, 'DAY') AS current_day,
    TO_CHAR(SYSDATE, 'D') AS day_number,
    CASE 
        WHEN TO_CHAR(SYSDATE, 'D') IN ('1', '7') THEN 'WEEKEND - Operations ALLOWED'
        WHEN TO_CHAR(SYSDATE, 'D') BETWEEN '2' AND '6' THEN 'WEEKDAY - Operations DENIED'
    END AS operation_status,
    SYSDATE AS current_datetime
FROM DUAL;
```
   

![TEST CURRENT DAY OPERATION](https://github.com/user-attachments/assets/d5fd7db7-88b2-4f32-b634-8bdf98f9086f)


#### Test Query 2: Check upcoming holidays


PROMPT
PROMPT Upcoming Public Holidays:

```SQL

SELECT holiday_name, TO_CHAR(holiday_date, 'DAY DD-MON-YYYY') AS holiday_date
FROM public_holidays
WHERE holiday_date >= TRUNC(SYSDATE)
ORDER BY holiday_date;

```

![CHECKING UPCOMING HOLIDAYS](https://github.com/user-attachments/assets/e11815aa-8b38-4649-a4a4-2c81d5bfe3a5)


#### TEST 1: INSERT ON WEEKDAY (Should be DENIED)


PROMPT TEST 1: Attempting INSERT on current day...



```SQL

BEGIN
    INSERT INTO passengers (full_name, email, phone, passport_number, loyalty_points)
    VALUES ('Test Weekday User', 'test.weekday@test.com', '0788999999', 'TEST001', 100);
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('SUCCESS: INSERT was allowed');
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('DENIED: ' || SQLERRM);
        ROLLBACK;
END;
/

```
![WEEKDAY NOT ALLOWED](https://github.com/user-attachments/assets/9a32c5c5-ad19-499b-b1d1-e2f6ffe34ecf)


#### TEST 2: UPDATE ON CURRENT DAY

PROMPT TEST 2: Attempting UPDATE on current day...

```SQL

BEGIN
    UPDATE passe  ngers 
    SET loyalty_points = loyalty_points + 10
    WHERE passenger_id = 1;
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('SUCCESS: UPDATE was allowed');
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('DENIED: ' || SQLERRM);
        ROLLBACK;
END;
/

```

![UPDATE ALLOWED](https://github.com/user-attachments/assets/ec913eb6-1149-4fb9-88b9-1d66c2931a81)


#### TEST 3: DELETE ON CURRENT DAY


PROMPT TEST 3: Attempting DELETE on current day...

```SQL

BEGIN
    DELETE FROM bookings WHERE booking_id = 999;
    COMMIT;
    DBMS_OUTPUT.PUT_LINE('SUCCESS: DELETE was allowed');
EXCEPTION
    WHEN OTHERS THEN
        DBMS_OUTPUT.PUT_LINE('DENIED: ' || SQLERRM);
        ROLLBACK;
END;
/
```
![DELETE ALLOWED](https://github.com/user-attachments/assets/03287470-f95b-44f9-97d7-10987997c5a7)


#### AUDIT LOG QUERIES

PROMPT AUDIT LOG: All Operation Attempts (Last 10)

```SQL

SELECT 
    audit_id,
    table_name,
    operation_type,
    TO_CHAR(operation_date, 'YYYY-MM-DD HH24:MI:SS') AS operation_time,
    operation_day,
    username,
    attempt_status,
    SUBSTR(denial_reason, 1, 50) AS reason,
    session_id
FROM audit_log
ORDER BY audit_id DESC

FETCH FIRST 10 ROWS ONLY;
```


![AUDIT LOG QUERIES](https://github.com/user-attachments/assets/283755da-210c-4501-845f-19320c3d9f8c)


#### PROMPT AUDIT LOG: Denied Attempts Summary

```SQL
SELECT 
    table_name,
    operation_type,
    attempt_status,
    COUNT(*) AS attempt_count,
    MIN(operation_date) AS first_attempt,
    MAX(operation_date) AS last_attempt
FROM audit_log
GROUP BY table_name, operation_type, attempt_status
ORDER BY table_name, operation_type;

```

![AUDIT LOG QUERIES 2](https://github.com/user-attachments/assets/a24e73d6-a860-4627-bcb4-01e91f968f89)

```sql
SELECT 
    operation_day,
    attempt_status,
    COUNT(*) AS total_attempts
FROM audit_log
GROUP BY operation_day, attempt_status
ORDER BY 
    CASE operation_day
        WHEN 'SUNDAY' THEN 1
        WHEN 'MONDAY' THEN 2
        WHEN 'TUESDAY' THEN 3
        WHEN 'WEDNESDAY' THEN 4
        WHEN 'THURSDAY' THEN 5
        WHEN 'FRIDAY' THEN 6
        WHEN 'SATURDAY' THEN 7
    END;
```

![SUMMARY OF OPERATIONS BY DAY OF WEEKS](https://github.com/user-attachments/assets/b6a57fc6-55dc-4d5a-a6b8-38a6c4452601)


=










