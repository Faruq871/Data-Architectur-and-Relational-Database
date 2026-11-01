# SQL Data Architecture Projects

This repository contains relational database schemas designed for data engineering and analytics purposes. It currently houses two distinct projects: the **Olist Brazilian E-Commerce Schema** and the **Universe Schema** (FreeCodeCamp).

---

## Project 1: Olist Brazilian E-Commerce Relational Schema

A normalized relational database schema designed for the [Olist Brazilian E-Commerce Public Dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce). This project demonstrates data modeling, DDL implementation, and data migration best practices using MySQL.

### Dataset Reference
- **Source**: [Kaggle - Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)
- **Description**: A real-world dataset containing 100,000+ orders from 2016 to 2018, including customers, products, sellers, payments, and reviews.

### Schema Overview
The database consists of 8 core tables optimized for data integrity and analytical query performance:
- `customers`: Customer information and location.
- `geolocation`: Geographic coordinates mapped by zip code.
- `sellers`: Seller information and location.
- `products`: Product catalog and physical attributes.
- `orders`: Order header information and status timestamps.
- `order_items`: Line items within each order (fact table).
- `order_payments`: Payment details and methods per order.
- `order_reviews`: Customer review scores and feedback.

### Key Design Decisions
1. **Identifier Strategy**: `VARCHAR(32)` is used for all hashed IDs (e.g., `customer_id`, `order_id`) to exactly match the MD5 hash format of the source data without the overhead of `TEXT` or the risk of truncation.
2. **Geolocation Surrogate Key**: The original dataset contains multiple latitude/longitude pairs for a single zip code. A surrogate key (`geolocation_id INT AUTO_INCREMENT`) was implemented instead of a composite primary key to prevent index bloat and maintain schema cleanliness, as this table is not directly referenced via foreign keys by transactional tables.
3. **Composite Primary Keys**: Applied to `order_items` (`order_id`, `order_item_id`) and `order_payments` (`order_id`, `payment_sequential`) to accurately model one-to-many relationships where the child ID is only unique within the context of the parent order.
4. **Numeric Precision**: `DECIMAL(10,2)` for currency and `DECIMAL(9,6)` for coordinates to prevent floating-point precision errors inherent in `FLOAT` or `DOUBLE`.
5. **Referential Integrity**: `ON DELETE CASCADE` is applied to child tables of `orders` to prevent orphan records. `ON DELETE RESTRICT` is applied to master tables (`products`, `customers`, `sellers`) to protect business-critical data.

### Setup & Data Import Instructions
1. **Create Schema**: Execute `schema.sql` in MySQL Workbench or via CLI (`mysql -u root -p < schema.sql`).
2. **Import Data**: Use the MySQL Workbench **Table Data Import Wizard** (Right-click table > Table Data Import Wizard) to load the corresponding CSV files from the Kaggle dataset.
   - *Critical Note for `geolocation`*: During column mapping, set the `geolocation_id` target column to **<Skip>** so MySQL can auto-generate it via `AUTO_INCREMENT`.
3. **Import Order**: To respect foreign key dependencies, import in this order: `geolocation` → `customers` → `sellers` → `products` → `orders` → `order_items` → `order_payments` → `order_reviews`.

---

## Project 2: Universe Schema (FreeCodeCamp)

This repository includes `universe.sql`, a complete PostgreSQL database dump from the FreeCodeCamp Relational Database Certification project. 

- **Purpose**: Demonstrates foundational mastery of relational database design, including table creation, data types, and referential integrity, using an astronomical domain model.
- **Key Features**: 
  - Implements PostgreSQL-specific `SEQUENCE` objects for auto-incrementing primary keys.
  - Enforces data integrity via `UNIQUE` constraints on celestial names and strict `FOREIGN KEY` hierarchies (Galaxy → Star/Black Hole → Planet → Moon).
  - Includes pre-populated sample data for immediate querying and testing.
- **Engine**: PostgreSQL 12+

---

## Repository Structure

```text
.
├── schema.sql          
├── universe.sql        
├── erd_diagram.png     
└── README.md           
