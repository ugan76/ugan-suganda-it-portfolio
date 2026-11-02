# 06 — Database Administration with MariaDB

**Category:** Database Administration  
**Status:** Documentation template — evidence to be added

## 1. Project Overview

Document the installation and administration of MariaDB in a lab environment,
including database creation, user privileges, SQL operations, and verification.

## 2. Objectives

- Install and verify MariaDB Server.
- Create a sample database and tables.
- Manage database users and privileges.
- Run basic SQL operations.
- Verify service health and database connectivity.

## 3. Lab Environment

| Component | Actual value |
|---|---|
| Host OS | [TODO] |
| MariaDB version | [TODO] |
| Client tool | [TODO] |
| Database purpose | [TODO — sample data only] |

## 4. Implementation

[TODO: document installation, service management, database and table creation,
user/privilege setup, and SQL statements actually used.]

Example SQL using a non-sensitive sample database:

```sql
CREATE DATABASE portfolio_demo;
USE portfolio_demo;

CREATE TABLE test_items (
    id INT AUTO_INCREMENT PRIMARY KEY,
    item_name VARCHAR(100) NOT NULL
);

INSERT INTO test_items (item_name) VALUES ('Test record');
SELECT * FROM test_items;
```

## 5. Testing and Results

| Test | Method | Actual result |
|---|---|---|
| Service active | `systemctl status mariadb` | [TODO] |
| Database exists | `SHOW DATABASES;` | [TODO] |
| Table exists | `SHOW TABLES;` | [TODO] |
| CRUD query | SQL test | [TODO] |
| Access control | Test authorized user | [TODO] |

## 6. Evidence

Add screenshots of service status, sample schema, SQL execution, and test results.
Use synthetic data only.

## 7. Troubleshooting and Lessons Learned

[TODO]

## 8. Security Notes

Never publish database passwords, connection strings with credentials, production
data, private backups, or user information.
