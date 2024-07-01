# SQL Injection: Potential risks and effective prevention methods

<small>*Updated date: May 16, 2024*</small>

## Content of the post:
[I. What is SQL Injection (SQLi)?](#what-is-sql-injection)

[II. How it works.](#how-it-works)

[III. Common types of SQLi attacks.](#common-types-of-sqli-attacks)
- [1. In-band SQLi](#in-band-sqli)
- [2. Inferential SQLi (Blind SQLi)](#inferential-sqli)
- [3. Out-of-band SQLi](#out-of-band-sqli)

[IV. Main cause of SQL Injection (SQLi) vulnerability.](#main-cause-of-sql-injection-sqli-vulnerability)
- [1. Using unsafe SQL queries](#using-unsafe-sql-queries)
- [2. Lack of checking and filtering input data](#lack-of-checking-and-filtering-input-data)
- [3. Using dangerous SQL functions](#using-dangerous-sql-functions)
- [4. Weak authentication and session management](#weak-authentication-and-session-management)
- [5. Using outdated software](#using-outdated-software)
- [6. Lack of security awareness](#lack-of-security-awareness)

[V. Serious consequences of SQL Injection (SQLi) attack.](#serious-consequences-of-sql-injection-sqli-attack)
- [1. Data loss](#data-loss)
- [2. Operational interruption](#operational-interruption)
- [3. Damage to reputation](#damage-to-reputation)
- [4. Fine](#fine)
- [5. Initiation for other attacks](#initiation-for-other-attacks)

[VI. How to effectively prevent SQL Injection (SQLi) attacks.](#how-to-effectively-prevent-sql-injection-sqli-attacks)
- [1. Use prepared parameters](#use-prepared-parameters)
- [2. Perform checking and filtering of input data](#perform-checking-and-filtering-of-input-data)
- [3. Limit the use of dangerous SQL functions](#limit-the-use-of-dangerous-sql-functions)
- [4. Use a strong authentication and session management system](#use-a-strong-authentication-and-session-management-system)
- [5. Update software regularly](#update-software-regularly)
- [6. Raise security awareness](#raise-security-awareness)
- [7. Use a security scanning tool](#use-a-security-scanning-tool)
- [8. Perform penetration testing](#perform-penetration-testing)

<a name="what-is-sql-injection"></a>
## I. What is SQL Injection (SQLi)? 
`SQL Injection` (Sequel Injection), abbreviated as `SQLi`, is a dangerous cyber attack technique that allows attackers to take advantage of security vulnerabilities in web applications to execute unauthorized SQL statements. The attacker's goal is to steal sensitive data, sabotage the database or gain access to the system.

In many cases, `SQL Injection (SQLi)` can be exploited for more serious escalation, beyond normal data exfiltration:

- **Privilege Escalation:**
    - **Sensitive information exposed:** Sometimes, an attacker's SQLi query can accidentally reveal information about the system configuration, such as the username and permissions of the database account being used.
    - **Search for privileged accounts:** With this information, attackers can try to find and exploit accounts with higher rights in the database system, such as `root` accounts or administrative accounts.
    - **Execute commands on the operating system:** If an attacker succeeds in gaining access to a privileged account, they can use that permission to execute commands on the server's operating system itself, allowing they install malware, delete data, or take complete control of the system.

- **Denial of service (DoS) attack:**
    - **Resource abuse:** An attacker can use SQLi to make a large number of unnecessary or complex queries to the database.
    - **System overload:** This can overload the database server's resources, causing it to run slowly or even stop working completely, preventing legitimate users from accessing the service.
    - **Large-scale DoS attack:** In some cases, an attacker can combine SQLi with other techniques to create a larger-scale DoS attack, affecting the entire system or network.

## II. How it works. <a name="how-it-works"></a>
- **Attacker identifies vulnerability:** Attackers look for weaknesses in web applications where they can inject malicious SQL code. This vulnerability often appears in data entry forms where users provide information such as login names, passwords or payment information.
- **Malicious SQL code injection:** The attacker inserts malicious SQL code into the application's input data. This code can be hidden with special characters or encrypted to bypass basic security measures.
- **SQL Code Execution:** When the web application processes input data, malicious SQL code is executed on the database. This allows attackers to perform many malicious actions, including:
    - **Data theft:** An attacker can access and copy sensitive data from a database, such as user account information, financial information, or company secrets.
    - **Data modification:** An attacker can change or delete data in the database, causing damage or business disruption.
    - **Launch other attacks:** An attacker can use access to the database to execute other attacks, such as a ransomware attack or a denial of service (DoS) attack as explained above.

For example, suppose a website has a simple login form for users to enter a username and password similar to the following query statement:
```sql
CREATE FUNCTION GetUserInformation(p_username VARCHAR(255), p_password VARCHAR(255))
RETURNS TABLE
AS
RETURN (
SELECT * FROM users
WHERE username = p_username AND password = p_password
);

```
This form can be vulnerable to SQLi if it is not properly protected. An attacker could send a login request with the username `admin' OR 1=1; --` and enter any password:
```sql
SELECT * FROM users
WHERE username = 'admin' OR 1=1; -- ' AND password = 'abc123'

```
This malicious SQL code will fool the web application into believing that the user is trying to log in with the username `admin` and any password. However, because the single quote `'` after `admin` ends an input string and the double dash `--` is a comment in SQL, the entire code behind is disabled. This code is always correct because even if the username is wrong, the `OR 1=1` condition will return `TRUE`, anyone can get the data or log in to the website using a username and password like this.

<a name="common-types-of-sqli-attacks"></a>
## III. Common types of SQLi attacks. 

<a name="in-band-sqli"></a>
### 1. In-band SQLi 
This is the most common type of SQLi attack and is also the easiest to exploit. The attacker collects the results of the executed malicious SQL code through the web application's response. This response can include error messages, web page content, or even data from the database.

**In-band SQLi can be divided into two subtypes:**
- **Error-based SQLi:** Attackers use SQL errors to gather information about the database.
- **Union-based SQLi:** The attacker uses the `UNION SQL` operator to combine data from multiple tables in the database.

<a name="inferential-sqli"></a>
### 2. Inferential SQLi (Blind SQLi) 
This type of attack is more difficult to exploit than `in-band SQLi`, but it can still be dangerous. Attackers cannot gather direct results of malicious SQL code, but they can use inference techniques to gather information.

**Inferential SQLi can be divided into two subtypes:**
- **Blind-boolean-based SQLi:** The attacker uses an SQL query to determine whether each character in the input string is correct.
- **Time-based-blind SQLi:** Attacker uses web application response time to gather information.

<a name="out-of-band-sqli"></a>
### 3. Out-of-band SQLi 
This type of attack is less common than `in-band SQLi` and `blind SQLi`, but it can be very dangerous. The attacker uses malicious SQL code to interact with the external server and collect information. This allows attackers to perform actions they cannot perform with `in-band SQLi` or `blind SQLi`, such as sending spam emails or installing malware.

**There are also several other less common types of SQLi attacks, including:**
- **Second-order SQLi:** Attackers take advantage of data stored in the database to execute unauthorized SQL queries.
- **NoSQL injection:** Attackers target NoSQL databases, such as `MongoDB` or `Cassandra`.
- **XML injection:** Attackers target web applications that use XML to process data.

<a name="main-cause-of-sql-injection-sqli-vulnerability"></a>
## IV. Main cause of SQL Injection (SQLi) vulnerability. 
`SQL Injection (SQLi)` vulnerabilities appear due to many different factors, but the main cause is often due to errors in web application programming and management.

<a name="using-unsafe-sql-queries"></a>
### 1. Using unsafe SQL queries 
Using SQL queries built directly from user input without proper processing or validation is the most common cause of SQLi.

An attacker can easily insert malicious SQL code into the input data, causing the web application to execute that code on the database without even realizing it.

<a name="lack-of-checking-and-filtering-input-data"></a>
### 2. Lack of checking and filtering input data 
The web application does not carefully check and filter the input data to remove special characters and malicious code that could be used to execute SQLi.

An attacker could exploit this vulnerability to inject malicious SQL code into data fields, forms, or URLs, causing the web application to execute that code on the database.

<a name="using-dangerous-sql-functions"></a>
### 3. Using dangerous SQL functions 
Some SQL functions, such as `EXEC()`, `EXECUTE()`, `eval()`, can be used to execute any code on the server.

Using these functions without appropriate security measures can leave the web application vulnerable to SQLi attacks.

<a name="weak-authentication-and-session-management"></a>
### 4. Weak authentication and session management 
Weak authentication and session management systems could allow an attacker to spoof a legitimate user's identity and execute unauthorized SQL queries on the database.

<a name="using-outdated-software"></a>
### 5. Using outdated software 
Using outdated software, including web application software, databases, and operating systems, can leave web applications vulnerable to SQLi attacks due to known but unknown security vulnerabilities. patched.

<a name="lack-of-security-awareness"></a>
### 6. Lack of security awareness 
A lack of security awareness among both programmers and users can lead to flaws in web application design, development, and usage, facilitating SQLi attacks.

<a name="serious-consequences-of-sql-injection-sqli-attack"></a>
## V. Serious consequences of SQL Injection (SQLi) attack. 
`SQL Injection (SQLi)` attacks can cause many serious consequences for businesses and organizations, including:

<a name="data-loss"></a>
### 1. Data loss 
An attacker can access and copy sensitive data from the database, such as user account information, financial information, or company secrets.

Stolen data can be used for many illegal purposes, including identity fraud, financial theft or blackmail.

Data loss can cause financial, reputational and reputational damage to businesses and organizations.

<a name="operational-interruption"></a>
### 2. Operational interruption 
An attacker could modify or delete data in the database, causing business disruption.

For example, an attacker could delete product information, disable customer accounts, or disrupt the payment process.

Operational disruptions can cause financial losses and lost business opportunities for businesses and organizations.

<a name="damage-to-reputation"></a>
### 3. Damage to reputation 
Businesses and organizations can lose reputation due to data breaches and failure to protect customer information.

Reputational damage can lead to loss of customers, reduced revenue and difficulty attracting investment.

<a name="fine"></a>
### 4. Fine 
Businesses and organizations can be fined for violating data security regulations.

For example, the European Union's General Data Protection Regulation (GDPR) can fine violating businesses up to 4% of annual global turnover or EUR 20 million, whichever is higher.

<a name="initiation-for-other-attacks"></a>
### 5. Initiation for other attacks 
An attacker can use access to the database to carry out other attacks, such as malware propagation attacks or denial of service (DoS) attacks.

These attacks can cause additional damage to businesses and organizations.

**In addition, SQLi attacks can also cause other consequences such as:**
- Causes difficulty in complying with legal regulations
- Reduces employee productivity
- Causes stress and anxiety for customers

<a name="how-to-effectively-prevent-sql-injection-sqli-attacks"></a>
## VI. How to effectively prevent SQL Injection (SQLi) attacks.
To protect web applications from dangerous SQL Injection (SQLi) attacks, implementing effective prevention measures is extremely important. Here are some specific solutions with examples:

<a name="use-prepared-parameters"></a>
### 1. Use prepared parameters 
This is the most effective method to prevent SQLi. Instead of building SQL queries directly from user data, use prepared parameters to separate the data from the query. The database automatically processes input data, ensuring it does not contain malicious SQL code.

For example:
```sql
-- SQL code is insecure (can be subject to SQLi attacks)
$username = $_POST['username'];
$password = $_POST['password'];

$query = "SELECT * FROM users WHERE username='$username' AND password='$password'";
$result = mysqli_query($conn, $query);

```

```sql
-- Safe SQL code uses prepared parameters
$username = $_POST['username'];
$password = $_POST['password'];

$stmt = $conn->prepare("SELECT * FROM users WHERE username=? AND password=?");
$stmt->bind_param("ss", $username, $password);
$stmt->execute();
$result = $stmt->get_result();

```

<a name="perform-checking-and-filtering-of-input-data"></a>
### 2. Perform checking and filtering of input data 
Always check and filter user input before using it in an SQL query. Remove special characters, HTML code, JavaScript code, and any other characters that could harm the query.

For example (PHP):
```php
function sanitizeInput($data) {
  $data = trim($data);
  $data = stripslashes($data);
  $data = htmlspecialchars($data);
  $data = filter_var($data, FILTER_SANITIZE_STRING);
  return $data;
}

$username = sanitizeInput($_POST['username']);
$password = sanitizeInput($_POST['password']);

// Use filtered $username and $password in SQL queries

```

<a name="limit-the-use-of-dangerous-sql-functions"></a>
### 3. Limit the use of dangerous SQL functions 
Avoid using SQL functions such as `EXEC()`, `EXECUTE()`, `eval()` that may allow arbitrary code execution. Instead, use safer SQL functions and check input data carefully.

<a name="use-a-strong-authentication-and-session-management-system"></a>
### 4. Use a strong authentication and session management system 
Use a strong authentication and session management system to prevent attackers from spoofing legitimate user identities and executing unauthorized SQL queries. Apply measures such as password encryption, two-factor authentication, and reasonable session expiration.

<a name="update-software-regularly"></a>
### 5. Update software regularly 
Regularly update operating systems, web application software, and databases to patch discovered security vulnerabilities. Use the latest security patches to minimize the risk of SQLi attacks.

<a name="raise-security-awareness"></a>
### 6. Raise security awareness 
Train programmers, system administrators and users on security threats, including SQLi. Raise awareness about the importance of good coding practices and compliance with security procedures.

<a name="use-a-security-scanning-tool"></a>
### 7. Use a security scanning tool 
Use specialized security scanning tools to detect SQLi vulnerabilities in web applications. Regular scanning and timely remediation of vulnerabilities can help minimize the risk of attacks.

<a name="perform-penetration-testing"></a>
### 8. Perform penetration testing 
Perform periodic penetration testing to simulate real-life attacks, including SQLi attacks. This testing helps identify and fix potential weaknesses before attackers exploit them.


SQL Injection (SQLi) is a potentially dangerous threat to any web application. However, by proactively applying preventative measures and best security practices, businesses and organizations can build a strong bulwark that protects their data and systems.\
Remember, cybersecurity is a continuous journey, requiring constant effort and updates. Always be proactive and equip yourself with the necessary knowledge and tools to fight increasingly sophisticated cyber security threats and effectively protect data and systems.