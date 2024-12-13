# azure-mysql-compatibility-fixes
Azure MySQL Database Compatibility Documentation

This documentation provides a comprehensive guide for ensuring compatibility when running SQL queries on an Azure Database for MySQL server. It highlights common compatibility issues, their root causes, and step-by-step solutions to resolve them. This guide is based on a real-world scenario involving provisioning an Azure MySQL server, mirroring it to MySQL Workbench, and troubleshooting query execution errors.

# Problem Overview
1. Initial Scenario
- Executed SQL queries successfully on MySQL Workbench (local instance).
- Provisioned an Azure MySQL server and mirrored it to MySQL Workbench.
- Created a database on Azure MySQL and re-ran the original queries to create tables.

2. Observed Issues
- Unexpected Column Addition:
Azure MySQL automatically introduced a hidden column (my_row_id) as a primary key when the table was created without explicitly defining a primary key.

- Error: Multiple Primary Keys Defined:
Attempting to alter the table to add a primary key caused a conflict, as the hidden column (my_row_id) was already assigned the primary key role.

- Deprecated Feature Warning:
Display width for integer types (such as BIGINT(20)) caused warnings in Azure MySQL.

# Root Cause Analysis
- Azure MySQL’s Auto-Generated Primary Key
Azure Database for MySQL automatically adds a hidden column (that is my_row_id) as a primary key to tables created without explicitly defining a primary key. This behavior ensures:

- Efficient replication.
Smooth internal operations.
If you later attempt to define another primary key, Azure MySQL detects the conflict and throws an error indicating "multiple primary keys defined."

- Deprecated Integer Display Width
The display width feature for integer types (for instance INT(11) or BIGINT(20)) is deprecated in MySQL 8.0+ and Azure MySQL. It has no functional impact on storage or display, but including it causes warnings.

# Compatibility Fixes
1. Fix 1: Define Primary Keys Explicitly
To avoid Azure MySQL's hidden column behavior:

- Revise SQL Queries:

Define a primary key explicitly in the CREATE TABLE statement.
Remove any subsequent ALTER TABLE statements that attempt to add a primary key.

- Drop Auto-Generated Columns: 
If a hidden column (like my_row_id) is already created, drop it before redefining the primary key.

2. Fix 2: Remove Deprecated Display Width
- Revise Integer Data Types:
Remove display width specifications (e.g., (20)) from BIGINT, INT, etc.

- Impact:
Eliminates warnings related to deprecated features.
Ensures compatibility with MySQL 8.0+ and Azure MySQL.

3. Fix 3: Default Length for VARCHAR
For consistency, retain or explicitly specify VARCHAR lengths (like, VARCHAR(255)) where needed.

4. Fix 4: Execute Revised Queries on Azure MySQL:
Ensure all queries run successfully without errors or warnings.

# Final Outcome
- All queries execute successfully on Azure MySQL.
- No hidden columns (my_row_id) are introduced.
- No warnings or errors for deprecated features.
- Ensures consistent behavior across MySQL Workbench and Azure MySQL.

By revising SQL queries to define primary keys explicitly and removing deprecated features, we achieve compatibility with Azure MySQL while ensuring smooth and error-free execution across platforms.
