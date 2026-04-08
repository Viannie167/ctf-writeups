# 🚩 CTF Writeup: Breaking Access Control & Script Injection 🚩

### 🛠️ Summary ^~^
During this challenge, I identified two critical vulnerabilities:

Insecure Direct Object Reference (IDOR): Bypassing authorization to edit restricted pages. ✅

Stored Cross-Site Scripting (XSS): Injecting malicious scripts into the page creation flow. ✅

### 1. The IDOR Vulnerability (Insecure Direct Object Reference)
🧐 Discovery & Reconnaissance

While testing the "Create Page" functionality, I noticed the application assigned my new page a numeric ID: page11. 🤓

I realized the application was likely using an incremental naming convention. If my page was 11, and the UI only showed two other visible pages, where were pages 3 through 10? 

### 🔎 Methodology (Object Enumeration)

I began ID Enumeration—manually changing the URL to see how the server responded to different page numbers:

/page/4 → 404 Not Found

/page/5 → 403 Forbidden 🚫

A 403 Forbidden error is a massive "green flag" :>. It confirms the resource exists but your current user isn't allowed to see it. 😌

### ⚡ The Exploit (Authorization Bypass) 💥

I noticed that while I couldn't view page 5, I had the rights to edit my own page (edit/page11). I tested whether the server checked authorization on the edit route specifically.

The Logic Flaw: I manually changed the URL from /edit/page11 to /edit/page5.

The server failed to verify if the "Editor" (me) owned "Page 5." It simply saw a valid edit request and loaded the content—revealing the hidden flag. 🎰 ✅

### 💡 Education Corner: What is IDOR?
IDOR happens when an application provides direct access to objects based on user-supplied input. If the backend doesn't check if User A actually owns the resource they are requesting (like a bank statement or a private page), a hacker can simply "guess" numbers to access other people's data.

## 2. The Stored XSS (Cross-Site Scripting)
### 🧐 Discovery

Whenever an application allows a user to input text that is later displayed back to users, it is a candidate for XSS. I tested the "Create Page" input field with a basic payload:

JavaScript
<script>alert(meow)</script> 
### ⚡ The Exploit 💥

After saving the page and refreshing, a browser alert box popped up with my text of meow. This confirmed that the application was not sanitizing my input. It treated my script as actual code rather than plain text.

### ⚠️ The Risk

Because this script is saved on the server (Stored XSS), every single person who visits my page will execute that code in their browser. ‼️

Malicious Use Case: An attacker could replace alert(1) with a script that steals the visitor's Session Cookies, allowing the attacker to hijack their account.‼️‼️

### 💡 Education Corner: Why does this happen?
This occurs because the application lacks Output Encoding. To fix this, the developers should convert characters like < and > into their HTML entity equivalents (&lt; and &gt;), which tells the browser to "show the text" instead of "run the code."

### 🏆 Conclusion & Lessons Learned
This challenge highlighted that access control must be enforced at every single endpoint (both view and edit). It also reminded me that any user-controlled input is "evil" until proven otherwise through sanitization.

### Tools Used: 
- Manual URL Manipulation (Browser)
- Basic JavaScript Payloads :>

- Until next time ^~^
