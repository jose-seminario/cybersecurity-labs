# SQL Injection - Retrieving Hidden Data

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection  
**Difficulty:** Apprentice  
**Status:** ✅ Solved

## Objective

Exploit a SQL Injection in the product category filter to retrieve unreleased products.

## 1. Initial Access

I started by accessing the lab and inspecting the available product categories.

<img width="1600" height="763" alt="image" src="https://github.com/user-attachments/assets/86e9ed09-1f3a-498e-a541-fac19f1174b4" />

## 2. Identifying the Injection Point

I selected the **Gifts** category and noticed that the category was directly reflected in the URL:

`/filter?category=Gifts`

<img width="1134" height="497" alt="image" src="https://github.com/user-attachments/assets/f5a1a163-93c1-41ca-94a4-96ebdcf7736c" />

This indicated that the `category` parameter was a possible point of injection.

## 3. SQL Injection

I modified the `category` parameter to alter the SQL query.

The injection used was:

`'+OR+1=1--`

<img width="1127" height="531" alt="image" src="https://github.com/user-attachments/assets/95cbc60b-f7a2-4f5a-88de-b59e9e8e2024" />

The application then displayed the lab success message, confirming that the SQL Injection allowed the retrieval of hidden data.

## What I Learned

- How to identify a possible SQL Injection point.
- How `OR 1=1` can make a condition always true.
- How `--` can comment out the rest of a SQL query.
- How SQL Injection can be used to retrieve hidden data.

## Result

✅ Lab successfully completed.
