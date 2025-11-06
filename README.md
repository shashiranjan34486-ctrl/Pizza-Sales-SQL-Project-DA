# Pizza-Sales-SQL-Project-DA
# 🍕 Pizza Sales SQL Analysis

## 📘 Project Overview
This project analyzes pizza sales data using **SQL** to extract meaningful business insights such as total revenue, top-selling pizzas, and order patterns.  
It demonstrates SQL proficiency — including joins, aggregation, ranking, and window functions — applied to a real-world sales dataset.

---

## 📂 Dataset Description

The dataset contains **four related tables**, stored as CSV/XLSX files:

| Table Name | Description | Key Columns |
|------------|-------------|-------------|
| **orders** | Order-level information (each row = one order) | `order_id`, `date`, `time` |
| **order_details** | Line-items for each order (each row = one pizza in an order) | `order_details_id`, `order_id`, `pizza_id`, `quantity` |
| **pizzas** | Menu items with size and price | `pizza_id`, `pizza_type_id`, `size`, `price` |
| **pizza_types** | Pizza type metadata (name, category, ingredients) | `pizza_type_id`, `name`, `category`, `ingredients` |

**Entity relationships (high-level):**

```
pizza_types (1) ───< pizzas (many) ───< order_details (many) >─── orders (1)
```

---

## 🧩 Database Schema

Use this schema to create the tables before running queries:

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
```

---

## 🎯 Project Objectives

The main objectives of this SQL project are to:

- Retrieve key performance metrics from pizza sales data.  
- Analyze ordering behavior and revenue generation.  
- Identify top-selling pizza types, sizes, and categories.  
- Calculate performance insights for business decision-making.

---

## 🟢 Basic SQL Queries

### 1️. Total number of orders placed
```sql
SELECT COUNT(DISTINCT order_id) AS total_orders
FROM orders;
```

### 2️. Total revenue generated from pizza sales
```sql
select round(sum(order_details.quantity * pizzas.price)) as total_revenue
from order_details, pizzas
where order_details.pizza_id = pizzas.pizza_id;
```

### 3️. Highest-priced pizza
```sql
SELECT pizza_id, price
FROM pizzas
ORDER BY price DESC
LIMIT 1;
```

### 4️. Most common pizza size ordered
```sql
SELECT p.size, COUNT(*) AS order_count
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
GROUP BY p.size
ORDER BY order_count DESC
LIMIT 1;
```

### 5️. Top 5 most ordered pizza types
```sql
SELECT pt.name, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY total_quantity DESC
LIMIT 5;
```

---

## 🟡 Intermediate SQL Queries

### 6️. Total quantity of each pizza category ordered
```sql
SELECT pt.category, SUM(od.quantity) AS total_quantity
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category
ORDER BY total_quantity DESC;
```

### 7️. Distribution of orders by hour of the day
```sql
SELECT EXTRACT(HOUR FROM time) AS order_hour,
       COUNT(order_id) AS total_orders
FROM orders
GROUP BY order_hour
ORDER BY order_hour;
```

### 8️. Category-wise distribution of pizzas
```sql
SELECT pt.category, COUNT(p.pizza_id) AS total_pizzas
FROM pizzas p
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category;
```

### 9️. Average number of pizzas ordered per day
```sql
SELECT ROUND(SUM(od.quantity) / COUNT(DISTINCT o.date), 2) AS avg_pizzas_per_day
FROM order_details od
JOIN orders o ON od.order_id = o.order_id;
```

### 10. Top 3 most ordered pizza types based on revenue
```sql
SELECT pt.name, ROUND(SUM(od.quantity * p.price), 2) AS revenue
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.name
ORDER BY revenue DESC
LIMIT 3;
```

---

## 🔴 Advanced SQL Queries

### 11. Percentage contribution of each pizza category to total revenue
```sql
SELECT pt.category,
       ROUND(SUM(od.quantity * p.price) * 100.0 /
             (SELECT SUM(od2.quantity * p2.price)
              FROM order_details od2
              JOIN pizzas p2 ON od2.pizza_id = p2.pizza_id), 2) AS percent_contribution
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
GROUP BY pt.category
ORDER BY percent_contribution DESC;
```

### 12. Cumulative revenue generated over time
```sql
SELECT o.date,
       SUM(SUM(od.quantity * p.price)) OVER (ORDER BY o.date) AS cumulative_revenue
FROM order_details od
JOIN pizzas p ON od.pizza_id = p.pizza_id
JOIN orders o ON od.order_id = o.order_id
GROUP BY o.date
ORDER BY o.date;
```

### 1️3. Top 3 most ordered pizza types by revenue for each category
```sql
SELECT category, name, revenue
FROM (
    SELECT pt.category,
           pt.name,
           SUM(od.quantity * p.price) AS revenue,
           RANK() OVER (PARTITION BY pt.category ORDER BY SUM(od.quantity * p.price) DESC) AS rank
    FROM order_details od
    JOIN pizzas p ON od.pizza_id = p.pizza_id
    JOIN pizza_types pt ON p.pizza_type_id = pt.pizza_type_id
    GROUP BY pt.category, pt.name
) ranked
WHERE rank <= 3;
```

---

## 📊 Key Insights (Example Results)

| Metric | Value (Approx.) |
|--------|------------------|
| **Total Orders** | 21,000+ |
| **Total Revenue** | $817,000 |
| **Most Common Size** | Large (L) |
| **Top Pizza Type** | Thai Chicken Pizza |
| **Top Category by Revenue** | Classic Pizzas |

---

## 🧾 Tools Used

- PostgreSQL – Query Execution  
- Excel / CSV – Data Source  
- SQL – Data Cleaning, Joins, Aggregations  
- GitHub – Project Documentation

---

## 🚀 How to Run This Project

1. Import all `.csv` / `.xlsx` files into your SQL database (PostgreSQL / MySQL / SQLite).  
2. Create tables using the provided schema.  
3. Run queries from Basic → Intermediate → Advanced to reproduce the analysis.  
4. (Optional) Export results and visualize in Power BI, Excel, or Google Sheets.

---

## 🧠 Learning Outcomes

Through this project, you will:

- Strengthen SQL querying and data analysis skills.  
- Understand joins, grouping, and window functions.  
- Translate business problems into SQL-based insights.

---

## 🧾 Database Schema (ERD) for dbdiagram.io

Use the following dbdiagram.io code to generate an ERD image:

```dbml
Table pizza_types {
  pizza_type_id varchar [pk]
  name varchar
  category varchar
  ingredients text
}

Table pizzas {
  pizza_id varchar [pk]
  pizza_type_id varchar [ref: > pizza_types.pizza_type_id]
  size varchar
  price decimal
}

Table order_details {
  order_details_id int [pk]
  order_id int
  pizza_id varchar [ref: > pizzas.pizza_id]
  quantity int
}

Table orders {
  order_id int [pk]
  date date
  time time
}
```

---

## 🧑‍💻 Author

**Shashi Ranjan**  
📍 Intern at HFFC  
💼 Exploring roles in Business Analytics & Data Analysis  
📧 www.linkedin.com/in/shashi-ranjan-7b6097282

---



