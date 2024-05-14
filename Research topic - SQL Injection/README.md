# SQL Injection: Potential risks and effective prevention methods

<small>*Updated date: May 16, 2024*</small>

## Content of the post:
[I. What is SQL Injection (SQLi)?](#I)

[II. How it works.](#II)

[III. Common types of SQLi attacks.](#III)
- [1. In-band SQLi](#III.1)
- [2. Inferential SQLi (Blind SQLi)](#III.2)
- [3. Out-of-band SQLi](#III.3)

[IV. Main cause of SQL Injection (SQLi) vulnerability.](#IV)
- [1. Using unsafe SQL queries](#IV.1)
- [2. Lack of checking and filtering input data](#IV.2)
- [3. Using dangerous SQL functions](#IV.3)
- [4. Weak authentication and session management](#IV.4)
- [5. Using outdated software](#IV.5)
- [6. Lack of security awareness](#IV.6)

[V. Serious consequences of SQL Injection (SQLi) attack.](#V)
- [1. Data loss](#V.1)
- [2. Operational interruption](#V.2)
- [3. Damage to reputation](#V.3)
- [4. Fine](#V.4)
- [5. Initiation for other attacks](#V.5)

[VI. How to effectively prevent SQL Injection (SQLi) attacks.](#VI)
- [1. Use prepared parameters](#VI.1)
- [2. Perform checking and filtering of input data](#VI.2)
- [3. Limit the use of dangerous SQL functions](#VI.3)
- [4. Use a strong authentication and session management system](#VI.4)
- [5. Update software regularly](#VI.5)
- [6. Raise security awareness](#VI.6)
- [7. Use a security scanning tool](#VI.7)
- [8. Perform penetration testing](#VI.8)

## I. What is SQL Injection (SQLi)? <a name="I"></a>
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

## II. How it works. <a name="II"></a>
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

## III. Common types of SQLi attacks. <a name="III"></a>

### 1. In-band SQLi <a name="III.1"></a>
This is the most common type of SQLi attack and is also the easiest to exploit. The attacker collects the results of the executed malicious SQL code through the web application's response. This response can include error messages, web page content, or even data from the database.

**In-band SQLi can be divided into two subtypes:**
- **Error-based SQLi:** Attackers use SQL errors to gather information about the database.
- **Union-based SQLi:** The attacker uses the `UNION SQL` operator to combine data from multiple tables in the database.

### 2. Inferential SQLi (Blind SQLi) <a name="III.2"></a>
This type of attack is more difficult to exploit than `in-band SQLi`, but it can still be dangerous. Attackers cannot gather direct results of malicious SQL code, but they can use inference techniques to gather information.

**Inferential SQLi can be divided into two subtypes:**
- **Blind-boolean-based SQLi:** The attacker uses an SQL query to determine whether each character in the input string is correct.
- **Time-based-blind SQLi:** Attacker uses web application response time to gather information.

### 3. Out-of-band SQLi <a name="III.3"></a>
This type of attack is less common than `in-band SQLi` and `blind SQLi`, but it can be very dangerous. The attacker uses malicious SQL code to interact with the external server and collect information. This allows attackers to perform actions they cannot perform with `in-band SQLi` or `blind SQLi`, such as sending spam emails or installing malware.

There are also several other less common types of SQLi attacks, including:
- **Second-order SQLi:** Attackers take advantage of data stored in the database to execute unauthorized SQL queries.
- **NoSQL injection:** Attackers target NoSQL databases, such as `MongoDB` or `Cassandra`.
- **XML injection:** Attackers target web applications that use XML to process data.

## IV. Main cause of SQL Injection (SQLi) vulnerability. <a name="IV"></a>
`SQL Injection (SQLi)` vulnerabilities appear due to many different factors, but the main cause is often due to errors in web application programming and management.

### 1. Using unsafe SQL queries <a name="IV.1"></a>
Using SQL queries built directly from user input without proper processing or validation is the most common cause of SQLi.

An attacker can easily insert malicious SQL code into the input data, causing the web application to execute that code on the database without even realizing it.

### 2. Lack of checking and filtering input data <a name="IV.2"></a>
The web application does not carefully check and filter the input data to remove special characters and malicious code that could be used to execute SQLi.

An attacker could exploit this vulnerability to inject malicious SQL code into data fields, forms, or URLs, causing the web application to execute that code on the database.

### 3. Using dangerous SQL functions <a name="IV.3"></a>
Some SQL functions, such as `EXEC()`, `EXECUTE()`, `eval()`, can be used to execute any code on the server.

Using these functions without appropriate security measures can leave the web application vulnerable to SQLi attacks.

### 4. Weak authentication and session management <a name="IV.4"></a>
Weak authentication and session management systems could allow an attacker to spoof a legitimate user's identity and execute unauthorized SQL queries on the database.

### 5. Using outdated software <a name="IV.5"></a>
Using outdated software, including web application software, databases, and operating systems, can leave web applications vulnerable to SQLi attacks due to known but unknown security vulnerabilities. patched.

### 6. Lack of security awareness <a name="IV.6"></a>
A lack of security awareness among both programmers and users can lead to flaws in web application design, development, and usage, facilitating SQLi attacks.

## V. Serious consequences of SQL Injection (SQLi) attack. <a name="V"></a>
`SQL Injection (SQLi)` attacks can cause many serious consequences for businesses and organizations, including:

### 1. Data loss <a name="V.1"></a>
An attacker can access and copy sensitive data from the database, such as user account information, financial information, or company secrets.

Stolen data can be used for many illegal purposes, including identity fraud, financial theft or blackmail.

Data loss can cause financial, reputational and reputational damage to businesses and organizations.

### 2. Operational interruption <a name="V.2"></a>
An attacker could modify or delete data in the database, causing business disruption.

For example, an attacker could delete product information, disable customer accounts, or disrupt the payment process.

Operational disruptions can cause financial losses and lost business opportunities for businesses and organizations.

### 3. Damage to reputation <a name="V.3"></a>
Businesses and organizations can lose reputation due to data breaches and failure to protect customer information.

Reputational damage can lead to loss of customers, reduced revenue and difficulty attracting investment.

### 4. Fine <a name="V.4"></a>
Businesses and organizations can be fined for violating data security regulations.

For example, the European Union's General Data Protection Regulation (GDPR) can fine violating businesses up to 4% of annual global turnover or EUR 20 million, whichever is higher.

### 5. Initiation for other attacks <a name="V.5"></a>
An attacker can use access to the database to carry out other attacks, such as malware propagation attacks or denial of service (DoS) attacks.

These attacks can cause additional damage to businesses and organizations.

**In addition, SQLi attacks can also cause other consequences such as:**
- Causes difficulty in complying with legal regulations
- Reduces employee productivity
- Causes stress and anxiety for customers

## VI. How to effectively prevent SQL Injection (SQLi) attacks. <a name="VI"></a>
To protect web applications from dangerous SQL Injection (SQLi) attacks, implementing effective prevention measures is extremely important. Here are some specific solutions with examples:

### 1. Use prepared parameters <a name="VI.1"></a>
This is the most effective method to prevent SQLi. Instead of building SQL queries directly from user data, use prepared parameters to separate the data from the query. The database automatically processes input data, ensuring it does not contain malicious SQL code.

For example:
```sql
// SQL code is insecure (can be subject to SQLi attacks)
$username = $_POST['username'];
$password = $_POST['password'];

$query = "SELECT * FROM users WHERE username='$username' AND password='$password'";
$result = mysqli_query($conn, $query);

```

```sql
// Safe SQL code uses prepared parameters
$username = $_POST['username'];
$password = $_POST['password'];

$stmt = $conn->prepare("SELECT * FROM users WHERE username=? AND password=?");
$stmt->bind_param("ss", $username, $password);
$stmt->execute();
$result = $stmt->get_result();

```

### 2. Perform checking and filtering of input data <a name="VI.2"></a>
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
### 3. Limit the use of dangerous SQL functions <a name="VI.3"></a>
Avoid using SQL functions such as `EXEC()`, `EXECUTE()`, `eval()` that may allow arbitrary code execution. Instead, use safer SQL functions and check input data carefully.

### 4. Use a strong authentication and session management system <a name="VI.4"></a>
Use a strong authentication and session management system to prevent attackers from spoofing legitimate user identities and executing unauthorized SQL queries. Apply measures such as password encryption, two-factor authentication, and reasonable session expiration.

### 5. Update software regularly <a name="VI.5"></a>
Regularly update operating systems, web application software, and databases to patch discovered security vulnerabilities. Use the latest security patches to minimize the risk of SQLi attacks.

### 6. Raise security awareness <a name="VI.6"></a>
Train programmers, system administrators and users on security threats, including SQLi. Raise awareness about the importance of good coding practices and compliance with security procedures.

### 7. Use a security scanning tool <a name="VI.7"></a>
Use specialized security scanning tools to detect SQLi vulnerabilities in web applications. Regular scanning and timely remediation of vulnerabilities can help minimize the risk of attacks.

### 8. Perform penetration testing <a name="VI.8"></a>
Perform periodic penetration testing to simulate real-life attacks, including SQLi attacks. This testing helps identify and fix potential weaknesses before attackers exploit them.


#### SQL Injection (SQLi) is a potentially dangerous threat to any web application. However, by proactively applying preventative measures and best security practices, businesses and organizations can build a strong bulwark that protects their data and systems.

#### Remember, cybersecurity is a continuous journey, requiring constant effort and updates. Always be proactive and equip yourself with the necessary knowledge and tools to fight increasingly sophisticated cyber security threats and effectively protect data and systems.





