# Pizza-Sales-SQL-Project-DA
# 🍕 Pizza Sales SQL Analysis

## 📘 Project Overview
This project analyzes pizza sales data using **SQL** to extract meaningful business insights such as total revenue, top-selling pizzas, and order patterns.  
It demonstrates SQL proficiency — including joins, aggregation, ranking, and window functions — applied to a real-world sales dataset.

---

## 📂 Dataset Description

The dataset contains **four tables**, each stored as an Excel file:

| File Name | Description |
|------------|--------------|
| **orders** | Contains order-level information such as order ID, date, and time. |
| **order_details** | Contains individual pizza items for each order (linked via `order_id`). |
| **pizzas** | Contains pizza-level details such as pizza ID, size, and price. |
| **pizza_types** | Contains information about pizza categories and pizza names. |

---

## 🧩 Database Schema

```sql
-- orders table
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    date DATE,
    time TIME
);

-- order_details table
CREATE TABLE order_details (
    order_details_id INT PRIMARY KEY,
    order_id INT,
    pizza_id TEXT,
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);

-- pizzas table
CREATE TABLE pizzas (
    pizza_id TEXT PRIMARY KEY,
    pizza_type_id TEXT,
    size TEXT,
    price NUMERIC,
    FOREIGN KEY (pizza_type_id) REFERENCES pizza_types(pizza_type_id)
);

-- pizza_types table
CREATE TABLE pizza_types (
    pizza_type_id TEXT PRIMARY KEY,
    name TEXT,
    category TEXT,
    ingredients TEXT
);
