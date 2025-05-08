# Library Management System Database 

## Overview
This repository contains a complete MySQL database schema for a comprehensive Library Management System. The database supports all core library operations including catalog management, patron services, circulation, and financial transactions.

## Database Schema Details

### Core Entities

#### 1. Member Management
- **member_types**: Defines different membership tiers with varying privileges
- **library_members**: Stores all patron information with membership details

#### 2. Staff Management
- **staff_roles**: Defines staff positions and permissions
- **library_staff**: Contains staff accounts with authentication details

#### 3. Catalog Management
- **publishers**: Publisher information and contact details
- **categories**: Hierarchical book categorization system
- **authors**: Author biographical information
- **books**: Core book metadata including ISBN
- **book_copies**: Physical inventory tracking

### Relationship Tables
- **book_authors**: Many-to-many relationship between books and authors
- **book_categories**: Many-to-many relationship between books and categories

### Operational Tables
- **book_loans**: Circulation transactions with due dates
- **book_reservations**: Hold requests with expiry tracking
- **fines**: Financial transactions for policy violations

## Key Features

1. **Comprehensive Catalog System**
   - Support for multiple authors per book
   - Hierarchical category system
   - Detailed publisher information
   - Physical copy tracking with condition monitoring

2. **Patron Management**
   - Tiered membership system
   - Configurable loan limits and periods
   - Membership period tracking

3. **Circulation System**
   - Loan tracking with due dates
   - Reservation system with automatic expiry
   - Multi-staff workflow support

4. **Financial Tracking**
   - Fine calculation and tracking
   - Payment status monitoring
   - Staff accountability for transactions

## Installation Instructions

1. **Prerequisites**
   - MySQL Server 5.7 or later
   - Appropriate user privileges

2. **Database Setup**
   ```bash
   mysql -u [username] -p < library_management_system.sql
   ```

3. **Verification**
   ```sql
   SHOW TABLES;
   ```

## Sample Queries

### Active Loans Report
```sql
SELECT 
    b.title AS 'Book Title',
    CONCAT(m.first_name, ' ', m.last_name) AS 'Member',
    l.loan_date AS 'Checkout Date',
    l.due_date AS 'Due Date'
FROM book_loans l
JOIN book_copies c ON l.copy_id = c.copy_id
JOIN books b ON c.book_id = b.book_id
JOIN library_members m ON l.member_id = m.member_id
WHERE l.return_date IS NULL;
```

### Overdue Items
```sql
SELECT 
    b.title AS 'Overdue Book',
    CONCAT(m.first_name, ' ', m.last_name) AS 'Member',
    m.email AS 'Email',
    DATEDIFF(CURDATE(), l.due_date) AS 'Days Overdue'
FROM book_loans l
JOIN book_copies c ON l.copy_id = c.copy_id
JOIN books b ON c.book_id = b.book_id
JOIN library_members m ON l.member_id = m.member_id
WHERE l.return_date IS NULL AND l.due_date < CURDATE();
```

### Popular Books Report
```sql
SELECT 
    b.title AS 'Book',
    COUNT(l.loan_id) AS 'Total Loans'
FROM books b
JOIN book_copies c ON b.book_id = c.book_id
JOIN book_loans l ON c.copy_id = l.copy_id
GROUP BY b.book_id
ORDER BY COUNT(l.loan_id) DESC
LIMIT 10;
```

## Data Integrity Features

1. **Constraints**
   - Primary and foreign keys on all relationships
   - Unique constraints for ISBNs, emails, and barcodes
   - Check constraints for date validation and business rules

2. **Automation**
   - Timestamp tracking for all records
   - Automatic status updates
   - Conditional defaults

## Maintenance Considerations

1. **Indexing Strategy**
   - Consider adding indexes on frequently queried columns:
     - Member names and emails
     - Book titles and ISBNs
     - Loan dates and statuses

2. **Backup Recommendation**
   ```bash
   mysqldump -u [username] -p library_db > library_backup.sql
   ```

## License
This database schema is released under the MIT License. Feel free to use and modify for your projects.

## Support
For assistance with implementation or customization, please open an issue in the repository.