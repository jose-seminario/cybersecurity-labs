# SQL Injection - Login Bypass

**Platform:** PortSwigger Web Security Academy  
**Category:** SQL Injection  
**Difficulty:** Apprentice  
**Status:** ✅ Solved

## Objective

Exploit a SQL Injection vulnerability in the login functionality to access the `administrator` account without knowing its password.

## 1. Accessing the Login

I started by accessing the lab and navigating to **My account**, where the login functionality was available.

<img width="1600" height="767" alt="image" src="https://github.com/user-attachments/assets/5690fc7c-1e42-487d-a5e7-41fccb29faed" />

## 2. Testing the Login

I identified the username field as the injection point.

I used the following input:

`administrator' --`

For the password, I entered a random value.

The idea was to close the username value and use `--` to comment out the remaining part of the SQL query, including the password check.

<img width="1600" height="771" alt="image" src="https://github.com/user-attachments/assets/8aed5db5-31b0-4948-af1f-ba18581cf469" />

## 3. Administrator Access

The login was successful even though I did not know the administrator's password.

The application displayed the account as:

**administrator**

<img width="1600" height="770" alt="image" src="https://github.com/user-attachments/assets/bc386a7d-5f9e-45bc-be52-b92744086a18" />

## What I Learned

- How SQL Injection can modify the logic of a login query.
- How `'` can close the username value.
- How `--` can comment out the remaining SQL query.
- How this can allow a login check to be bypassed when user input is handled insecurely.

## Result

✅ Lab successfully completed.
