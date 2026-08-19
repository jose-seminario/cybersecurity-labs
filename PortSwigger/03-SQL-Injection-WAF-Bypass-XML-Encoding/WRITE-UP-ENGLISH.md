# SQL Injection with Filter Bypass via XML Encoding

**Platform:** PortSwigger Web Security Academy
**Category:** SQL Injection
**Difficulty:** Practitioner
**Status:** ✅ Solved

## Objective

Exploit a SQL Injection vulnerability in the stock check functionality to retrieve the credentials of the `administrator` user and access their account.

## 1. Accessing the Lab

I started by accessing the PortSwigger lab using Microsoft Edge.

The application contains a stock checking functionality where I could select a store and check the available stock for a product.

<img width="1600" height="831" alt="image" src="https://github.com/user-attachments/assets/06e31802-96ba-404c-a485-3f17fd97c54d" />

## 2. Testing the Requests with F12

At first, I did not have Burp Suite installed, so I tried to analyze and modify the requests using the browser's developer tools.

Using **F12 → Network**, I was able to find the request related to the stock check and modify values such as the `storeId`.

This allowed me to understand that the value was being sent to the server through an XML request.

<img width="1600" height="830" alt="image" src="https://github.com/user-attachments/assets/64b41dd0-4d5b-428b-877a-d8dcabe87b23" />

## 3. Confirming the Parameter

After modifying the request, I confirmed that the application was processing my input correctly.

The response changed according to the value I sent, which helped me identify `storeId` as an interesting parameter to test.

This was my first confirmation that I could control part of the request sent to the server.

<img width="1600" height="769" alt="image" src="https://github.com/user-attachments/assets/1a617330-1249-4827-9c88-59bf90ee95bd" />

## 4. Trying to Complete the SQL Injection

I continued testing the parameter and eventually reached the point where I needed to retrieve information from the `users` table.

I tried using `UNION SELECT` and also attempted to use XML entities to bypass the filter.

However, working directly from F12 became difficult, and I could not get the complete payload to return the usernames and passwords as expected.

The application was also detecting some of the SQL Injection attempts with:

```
Attack detected
```

At this point, I decided to stop trying to force the solution through the browser tools and use a dedicated web security tool instead.

<img width="875" height="476" alt="image" src="https://github.com/user-attachments/assets/34affb67-bd93-4957-a5dd-51a30fee1e24" />

## 5. Installing Burp Suite and Hackvertor

I installed Burp Suite Community Edition and the Hackvertor extension.

I repeated the same process I had previously done in Edge, but this time using Burp Suite to intercept and modify the HTTP requests.

The main feature I used was **Repeater**, which allowed me to modify the request and send it again without having to repeat the actions in the browser.

This made the testing process much easier to manage.

<img width="1600" height="877" alt="image" src="https://github.com/user-attachments/assets/60b9592f-4d82-481f-b678-09fddfa99d9c" />

## 6. Bypassing the Filter and Retrieving the Credentials

Using Burp Suite, I was able to work with the stock request more effectively.

I tested the `UNION SELECT` approach and confirmed that the application was vulnerable, but the WAF blocked the normal payload.

Since the request was being sent in XML, I used Hackvertor to encode the SQL Injection payload using XML entities.

The final idea was to use `UNION SELECT` to retrieve the `username` and `password` from the `users` table and concatenate both values into a single column.

The response finally returned the users from the database, including:

```
administrator~[password]
```

This gave me the credentials required to access the administrator account.

<img width="1597" height="888" alt="image" src="https://github.com/user-attachments/assets/273a5c33-fdb1-4d76-9bd0-21cf2eae25d9" />

## 7. Administrator Access

Finally, I used the credentials obtained through the SQL Injection to log in to the application as `administrator`.

The login was successful and the lab was completed.

<img width="1600" height="684" alt="image" src="https://github.com/user-attachments/assets/15097723-fa10-4f55-90b5-8b9255db8dcd" />

## What I Learned

- How to analyze HTTP requests using browser developer tools.
- How Burp Suite can intercept and modify HTTP requests.
- How Repeater makes testing requests much easier.
- How a SQL Injection vulnerability can exist in an XML parameter.
- How `UNION SELECT` can be used to retrieve information from another table.
- How a WAF can detect common SQL Injection payloads.
- How XML encoding can be used to bypass the filter in this lab.
- How to concatenate values when only one column is available.

The most important thing I learned from this lab was how useful Burp Suite can be for web security testing. At first, I tried doing everything with F12, but when the requests became more complex, it was difficult to work efficiently.

After using Burp Suite, I understood that being able to intercept, modify, resend, and analyze HTTP requests makes a big difference when testing a web application.

## Conclusion

This lab let me practice SQL Injection in a more complete way.

I didn't just learn about `UNION SELECT` and extracting information from another table — I also learned about the process of analyzing an HTTP request, modifying it, and studying the response.

I also learned to use Burp Suite and Hackvertor, tools I hadn't used this way before.

I'm still learning, but this lab helped me understand much better how HTTP requests work and how they can be analyzed during a web security assessment.

## Result

✅ Lab successfully completed.
