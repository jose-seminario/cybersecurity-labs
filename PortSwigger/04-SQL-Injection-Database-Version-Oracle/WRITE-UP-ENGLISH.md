# SQL Injection Attack — Querying the Database Type and Version on Oracle

> **Lab:** PortSwigger Web Security Academy — SQL injection attack, querying the database type and version on Oracle
> **Category:** SQL Injection
> **Target DB:** Oracle 11g Express Edition

---

## 1. Lab Overview

The goal of this lab is to exploit a **SQL Injection** vulnerability so that the application queries information about the underlying Oracle database and reveals its version.

On loading the lab, an online shop is presented with several product categories. The application uses the `category` parameter to filter products.

<img width="1600" height="776" alt="image" src="https://github.com/user-attachments/assets/9e244a98-84fb-47d7-9887-754d2972d880" />

---

## 2. Intercepting the Request with Burp Suite

First, we enable **Intercept** in Burp Suite to capture and modify the HTTP requests sent by the browser.

When selecting a product category, Burp Suite intercepts the corresponding request.

<img width="593" height="227" alt="image" src="https://github.com/user-attachments/assets/c8a1885c-6c85-4a47-b2c1-d02e81fb1fe3" />

The request contains the parameter:

```text
category=Tech+gifts
```

This parameter becomes our entry point to test whether the application is vulnerable to SQL Injection.

---

## 3. Sending the Request to Repeater

Once the `GET` request is intercepted, we right-click it and select **Send to Repeater**.

This lets us work with the same request and modify the `category` parameter repeatedly without having to repeat the whole process from the browser.

<img width="1378" height="557" alt="image" src="https://github.com/user-attachments/assets/c9f49792-4999-4482-8242-06fc77ce804e" />

In Repeater, we can directly edit the parameter value and observe how the server's response changes.

---

## 4. Determining the Number of Columns in the Query

To perform a `UNION SELECT` attack, we first need to know **how many columns the original SQL query returns**.

This matters because the query we append via `UNION` must return the same number of columns as the original query.

Since this lab targets **Oracle**, we also need to account for its specific syntax, including the use of `FROM dual`.

The payload used was:

```sql
'+UNION+SELECT+'abc','def'+FROM+dual--
```

Here we attempt to return two values:

```text
abc → column 1
def → column 2
```

If the application displays both values, we can confirm that our `UNION SELECT` query is compatible with **two columns**, and that both can display text data.

---

## 5. Confirming the Two Columns

After sending the request, the server's response returns the values:

```text
abc
def
```

This confirms that the original query returns **two columns**, and that both can be used to return text-type data.

<img width="620" height="670" alt="image" src="https://github.com/user-attachments/assets/773ba936-e37d-4510-ab2c-791b49912224" />

It's important to understand that `abc` and `def` are just test values — we are not yet retrieving real database information, we're using them to confirm that both columns work correctly.

Additionally, `FROM dual` is required in this context because **DUAL is a special Oracle table** that allows executing a `SELECT` without querying an actual data table.

---

## 6. Retrieving the Oracle Version Information

Once we've confirmed there are two columns, we can use `UNION SELECT` to request real data from the database.

In Oracle, the view:

```text
v$version
```

contains information related to the database system's version.

Within it, we use the column:

```text
BANNER
```

which holds the information we want to retrieve.

The injection used is:

```sql
'+UNION+SELECT+BANNER,+NULL+FROM+v$version--
```

**Query breakdown:**

| Fragment | Purpose |
|---|---|
| `UNION SELECT` | Combines our query with the original one |
| `BANNER` | The version information we want to retrieve |
| `NULL` | Fills the second column needed to keep the same column count |
| `FROM v$version` | Specifies where to pull `BANNER` from |
| `--` | Comments out the rest of the original query |

<img width="1225" height="703" alt="image" src="https://github.com/user-attachments/assets/66985129-0f5a-4b04-b043-37dcf823e009" />

As a result, the application displays Oracle version information, including:

```text
Oracle Database 11g Express Edition
Release 11.2.0.2.0 - 64bit Production
```

<img width="1214" height="802" alt="image" src="https://github.com/user-attachments/assets/a758220d-6fe2-452a-9f10-9e6b8c962929" />


This demonstrates that we successfully leveraged the SQL Injection vulnerability to query database information.

**Note:** `v$version` is **not used across all databases** — it's an Oracle-specific feature. Other database engines use different queries to retrieve their version.

---

## 7. Lab Completed

Finally, the application confirms that the requested information was retrieved and displays the message:

> **"Congratulations, you solved the lab!"**

<img width="1467" height="611" alt="image" src="https://github.com/user-attachments/assets/11620aba-89a3-4cc5-b04e-8962a50692a0" />

---

## Conclusion

In this lab, I learned how to exploit a **SQL Injection vulnerability via `UNION SELECT`** to query information from an Oracle database.

**Process followed:**

```text
Intercept the request
        ↓
Send to Burp Repeater
        ↓
Identify the number of columns
        ↓
Confirm both columns accept text
        ↓
Use UNION SELECT
        ↓
Query BANNER from v$version
        ↓
Retrieve the Oracle version
        ↓
Lab completed
```

**Key takeaway:** before using `UNION SELECT`, you must determine the **number of columns returned by the original query** and identify which of those columns can be used to display the type of data you want to retrieve.
