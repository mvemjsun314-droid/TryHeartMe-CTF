# ❤️ TryHeartMe — TryHackMe Web CTF

> A hands-on TryHackMe web exploitation challenge focused on JWT authentication, improper token validation, and privilege escalation.

![Platform](https://img.shields.io/badge/Platform-TryHackMe-red)
![Category](https://img.shields.io/badge/Category-Web%20Security-blue)
![Focus](https://img.shields.io/badge/Focus-JWT%20%7C%20Authentication-orange)

---

## 🎯 Objective

The objective of this challenge was to find a way to purchase the hidden **ValenFlag** item from the TryHeartMe Valentine's Shop.

### Target

```text
10.48.177.31:5000 
```

### Skills Practiced

- Web reconnaissance
- Browser DevTools
- HTTP cookie analysis
- JWT analysis
- JWT manipulation
- Authentication and authorization testing
- Privilege escalation
- Web application security


🧭 Methodology

The challenge was approached using the following methodology:

* Identify the target web application.
* Create a user account and authenticate.
* Inspect the application using browser Developer Tools.
* Examine authentication cookies.
* Identify the JWT authentication cookie.
* Decode and analyze the JWT.
* Investigate authorization-related claims.
* Test the application's JWT validation.
* Modify the token in the controlled CTF environment.
* Replace the authentication cookie.
* Access the hidden ValenFlag item.
* Obtain the flag.


🔎 1. Web Application Reconnaissance

The challenge provided a Valentine's-themed web application.
The application contained functionality such as:

Shop
Login
Sign up
Account
Product pages

The main objective was to find a way to purchase the hidden ValenFlag item.

Target Application
http://10.48.177.31:5000


👤 2. Account Creation

I created a test account and logged into the application.
After authentication, I inspected the application using Firefox Developer Tools.


🍪 3. Inspecting Cookies

I opened:
Developer Tools → Storage → Cookies
I found an authentication cookie named:
tryheartme_jwt
The value of this cookie was a JSON Web Token (JWT).
A JWT normally consists of three parts:

HEADER.PAYLOAD.SIGNATURE
The cookie was therefore an important area to investigate.


🔐 4. JWT Analysis

I copied the JWT value and decoded it using:

CyberChef → JWT Decode

The decoded payload contained information similar to:

```json
{
  "email": "user@example.com",
  "role": "admin",
  "credits": 99999,
  "iat": 1789311972,
  "theme": "valentine"
}
```
Two fields were particularly interesting:

"role": "user",
"credits": 0

The role field appeared to be related to authorization, while credits controlled the account balance.


🚨 5. Identifying the Vulnerability

The next step was to determine whether the application properly validated the JWT before trusting its contents.

The application incorrectly accepted a JWT using the:

none

algorithm.

The JWT header could therefore be modified to:

{
  "alg": "none",
  "typ": "JWT"
}

The none algorithm represents a JWT without a cryptographic signature.

A secure application should reject such a token when the expected authentication mechanism requires a valid signature.


🧩 6. JWT Manipulation

I modified the JWT payload in the controlled TryHackMe environment.

The relevant claims were changed to:

{
  "email": "user@example.com",
  "role": "admin",
  "credits": 99999,
  "iat": 1789311972,
  "theme": "valentine"
}

The resulting token used an empty signature section:

HEADER.PAYLOAD.

Note: The actual JWT/token is intentionally not included in this write-up because authentication tokens should be treated as credentials.


🍪 7. Replacing the Authentication Cookie

I returned to:

Developer Tools → Storage → Cookies

and edited the:

tryheartme_jwt

cookie.

I replaced the original token with the modified token.

After refreshing the application, the modified token was accepted by the application.


⬆️ 8. Privilege Escalation

The application now treated the account as having elevated privileges.

The modified credit balance also allowed the account to purchase the hidden item.

I navigated back to the shop and accessed the ValenFlag item.

The flag was successfully obtained.

FLAG: [REDACTED]


💥 9. Impact

Improper JWT validation can allow an attacker to manipulate authentication or authorization information.

In this challenge, the vulnerability allowed:
- Modification of the user role
- Privilege escalation
- Modification of the account credit balance
- Access to restricted functionality
- Purchase of the hidden item

This demonstrates how incorrect JWT validation can result in an authentication/authorization bypass and privilege escalation.


🛡️ 10. Mitigation

A secure application should:

Properly verify JWT signatures.
Reject the none algorithm when it is not explicitly intended.
Enforce the expected signing algorithm server-side.
Validate important JWT claims.
Perform authorization checks on the server.
Never blindly trust client-controlled authorization data.
Use short-lived authentication tokens where appropriate.
Protect authentication cookies with appropriate security attributes.


🧠 11. What I Learned

This challenge helped me understand several important web-security concepts.
JWT Structure
A JWT consists of:
  Header.Payload.Signature

The payload is encoded, not encrypted, so sensitive information should not be placed there simply because it is inside a JWT.

Authentication vs Authorization

Authentication:    Who are you?
Authorization:     What are you allowed to do?

The challenge demonstrated how trusting authorization-related information from an improperly validated token can lead to privilege escalation.

### Developer Tools
I practiced using browser Developer Tools to:

Inspect cookies
Inspect storage
Examine network requests
Understand how authentication information is stored by a web application
Key Security Lesson

Client-controlled authentication data must never be blindly trusted.


🧰 12. Tools Used

| Tool | Purpose |
|---|---|
| TryHackMe AttackBox | CTF environment |
| Firefox Developer Tools | Cookie and web application inspection |
| CyberChef | JWT decoding and analysis |
| Browser | Application interaction |


🔗 13. Attack Chain

```text
Web Application
      ↓
Create Account / Login
      ↓
Developer Tools
      ↓
Authentication Cookie
      ↓
tryheartme_jwt
      ↓
JWT Decode
      ↓
Analyze JWT Claims
      ↓
Identify Improper Validation
      ↓
JWT Manipulation
      ↓
Privilege Escalation
      ↓
Access ValenFlag
      ↓
Capture Flag
```

📌14. Key Takeaway

The main lesson from TryHeartMe was understanding that JWTs are only secure when they are properly validated.
A server should never blindly trust authorization claims supplied by a client.

(Never trust → Always validate)
