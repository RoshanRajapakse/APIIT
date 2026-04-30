Lab Sheet — Application Security Testing (Static Analysis)

 1. Lab Overview

In the real world, when an organisation builds software — a banking app, a hospital management system, an e-commerce site — that software contains *thousands* of lines of code written by many people, often under time pressure. Hidden inside that code can be security weaknesses: small mistakes that attackers can exploit to steal data, take over accounts, or bring the system down.

In this lab, you will take a real codebase (ideally your project repo. for this course assessment), run an automated security scanner over it, examine what the scanner finds, and decide which of those findings are the *most important* problems that must be fixed first. You will then produce a short professional report explaining your work.



 2. Learning Outcomes

By the end of this lab, you will be able to:

1. Explain what Static Application Security Testing (SAST) is and why enterprises use it.
2. Install and run a SAST tool against a real software project.
3. Read and interpret a security scan report.
4. Distinguish a genuine security issue from a *false positive*.
5. Prioritise security findings using severity and exploitability.
6. Write a professional security finding report suitable for a development team.



 3. Background Concepts

# 3.1 What is Application Security?

Application security is the practice of finding and fixing weaknesses in software *before* attackers find them. A weakness in software is called a vulnerability. A real-world example: in 2017, the credit-reporting company Equifax was hacked because of a single unpatched vulnerability in their web application — over 145 million people's personal data was stolen.

# 3.2 SAST vs DAST (in plain English)

There are two main families of automated security testing:

- SAST — Static Application Security Testing. The tool reads the *source code* of the application without running it. Think of it like a spell-checker that reads through a book looking for grammatical mistakes — except instead of grammar, it looks for security mistakes.
- DAST — Dynamic Application Security Testing. The tool runs the application and tries to attack it from the outside, like a real hacker would.

This lab focuses on SAST. SAST is valuable because it can find problems *very early* — even before the software is finished — which is the cheapest time to fix bugs.

# 3.3 What is a Vulnerability?

A vulnerability is a flaw in code that an attacker could misuse. Common categories you will likely see in your scan:

| SQL Injection | The app builds a database query by sticking user input directly into it. An attacker can type in special text that tricks the database into giving up data it shouldn't. |
| Cross-Site Scripting (XSS) | The app displays user input on a web page without cleaning it. An attacker can insert JavaScript that runs in another user's browser. |
| Hardcoded Secrets | A password or API key is written directly into the code. Anyone who reads the code can see it. |
| Insecure Deserialization | The app accepts data from outside and converts it into objects without checking. Attackers can craft malicious data to take over the app. |
| Path Traversal | The app builds file paths from user input. An attacker can use `../../` to read files they shouldn't. |
| Weak Cryptography | The app uses outdated or broken algorithms (e.g., MD5, SHA-1) to protect data. |

# 3.4 Severity Levels

SAST tools might rank findings by severity, usually using CVSS (Common Vulnerability Scoring System):

- Critical (9.0 – 10.0): Could let an attacker fully take over the system. Fix immediately.
- High (7.0 – 8.9): Could lead to serious data loss or unauthorised access. Fix urgently.
- Medium (4.0 – 6.9): Limited impact, but still a real risk.
- Low (0.1 – 3.9): Minor issues, often best-practice violations.
- Informational: Not really a vulnerability — just something to be aware of.

# 3.5 What is a False Positive?

A false positive is when the tool says *"this is a vulnerability"* but, when you look closer, it actually isn't. SAST tools are conservative — they prefer to flag something suspicious rather than miss it — so they produce false positives. Part of being a good security engineer is being able to tell the difference.

Example:
A scanner flags `password = "admin123"` as a hardcoded password. But when you check the file, you see it's in a unit-test file, used to test a password-checker function, and never deployed. → False positive.

The same finding in a production configuration file → True positive, critical.



 4. SAST Tools You Can Use

You may use any SAST tool. Free / student-friendly options include:

| Semgrep ([semgrep.dev](https://semgrep.dev)) | 30+ languages | Free, fast, runs in the cloud or locally. Recommended for beginners. |
| SonarCloud ([sonarcloud.io](https://sonarcloud.io)) | 25+ languages | Free for public GitHub repos. Nice web dashboard. |
| Snyk Code ([snyk.io](https://snyk.io)) | Most major languages | Free tier, integrates with GitHub directly. |
| GitHub CodeQL (built into GitHub Advanced Security) | Many languages | Free for public repositories. Enable via "Security" tab. |
| Bandit ([github.com/PyCQA/bandit](https://github.com/PyCQA/bandit)) | Python only | Lightweight, runs from the command line. |
| SpotBugs + FindSecBugs | Java | Classic Java tooling. |
| Brakeman ([brakemanscanner.org](https://brakemanscanner.org)) | Ruby on Rails | Standard for Rails projects. |
| ESLint + `eslint-plugin-security` | JavaScript / Node.js | Easy if your project already uses ESLint. |



 5. The Codebase You Will Scan

You have two options.

# Option A — Use Your Own Project Repository
If you already have a coursework or capstone project on GitHub, scan that. This is the more valuable option because the findings are real and actionable.

# Option B — Use the Provided Vulnerable Repository
If you do not have a suitable project, use OWASP NodeGoat, a Node.js application maintained by the Open Worldwide Application Security Project:

> Repository: <https://github.com/OWASP/NodeGoat>
> Clone it with: `git clone https://github.com/OWASP/NodeGoat.git`

Alternative repos:
- OWASP WebGoat (Java) — <https://github.com/WebGoat/WebGoat>
- DVWA (PHP) — <https://github.com/digininja/DVWA>
- OWASP Juice Shop (Node.js / TypeScript) — <https://github.com/juice-shop/juice-shop>



 6. Lab Tasks (Step by Step)

> Take a screenshot at every step marked with — you will need them for your report.

# Task 1 — Prepare Your Workspace
1. Create a folder on your machine called `sast-lab`.
2. Inside it, clone the repository you chose (yours or NodeGoat).  *Screenshot of the cloned folder.*
3. Open it in any code editor (VS Code recommended).

# Task 2 — Choose and Install a SAST Tool
1. Pick one tool from Section 4.
2. Install it following the official instructions on the tool's website.
3. Verify it is installed by running its `--version` (or equivalent) command. 📸 *Screenshot of version output.*

# Task 3 — Run the Scan
1. Run the tool against your chosen codebase. Example commands:
   - Semgrep: `semgrep --config=auto .`
   - Bandit (Python): `bandit -r ./your_project`
   - Snyk Code: `snyk code test`
2. Wait for the scan to complete. Some scans take 1–2 minutes; cloud scans may take longer.
3. *Screenshot of the scan running and the summary at the end (number of issues found, severity breakdown).*

# Task 4 — Export the Full Output
1. Save the full scan report as a file. Most tools support `--json`, `--sarif`, or HTML output. Examples:
   - Semgrep: `semgrep --config=auto --json -o results.json .`
   - SonarCloud: download the report from the project dashboard.
2. Include this file (or a clear extract) as an appendix in your report.

# Task 5 — Triage: Pick the Top Critical Issues
1. Sort the findings by severity (Critical and High first).
2. From those, pick the 3 most critical issues that *you* would fix first.
3. For each one, record:
   - Title of the issue (e.g., "SQL Injection in `login.js`")
   - File path and line number
   - Severity rating as reported by the tool
   - The vulnerable code snippet (📸 screenshot or copy-paste)
   - A plain-English description of what the problem is
   - What an attacker could do if it is exploited

# Task 6 — Justify That They Are NOT False Positives
For *each* of your 3 chosen issues, explicitly explain why it is a real vulnerability and not a false positive. Use the checklist below as a guide:

- [ ] Is the vulnerable code in production / runtime code (not test code, not example code, not commented out)?
- [ ] Does the dangerous data come from an untrusted source (user input, network request, file upload, URL parameter)?
- [ ] Does that data reach a sensitive operation (database query, system command, file path, HTML output) without validation or sanitisation?
- [ ] Is there *no* mitigating control already in place (e.g., a framework that auto-escapes output)?
- [ ] Could you describe a realistic attack scenario in 1–2 sentences?

If you can answer yes to all five, the finding is almost certainly not a false positive.

# Task 7 — Compile the Report
Use the structure given in Section 7.

 7. Required Report Structure

Submit a short PDF (5–10 pages) containing:

1. Cover Page — Name, student ID, course, lab title, date.

2. Introduction (½ page) — One paragraph stating which repo you scanned and which tool you used, and why.

3. Methodology (1 page) — Step-by-step record of what you did, with the screenshots from Tasks 1–3.

4. Scan Output Summary (1 page) — A table or chart showing the total number of findings broken down by severity. Include a screenshot of the tool's summary view.

5. Top 3 Critical Findings (3–5 pages) — One sub-section per finding, each containing:
   - Finding title and severity
   - Location (file + line)
   - Vulnerable code snippet (screenshot)
   - Plain-English explanation
   - Realistic attack scenario
   - Why it is not a false positive (use the checklist from Task 6)


6. Reflection (½ page) — In your own words: What did you learn? Were you surprised by anything? How would SAST fit into a real software development process at a company?

7. Appendix — Full scan output file (or representative extract) and any extra screenshots.


Glossary

- CVE — Common Vulnerabilities and Exposures. A public list of known security flaws, each with an ID like `CVE-2021-44228`.
- CVSS — Common Vulnerability Scoring System. A 0–10 scale for how severe a vulnerability is.
- CWE — Common Weakness Enumeration. A catalogue of *types* of weaknesses (e.g., CWE-89 = SQL Injection).
- OWASP — Open Worldwide Application Security Project. A non-profit that publishes the famous "OWASP Top 10" list of the most common web vulnerabilities.
- Repository (repo) — A folder of code tracked by version control (Git), usually hosted on a site like GitHub.
- SARIF — Static Analysis Results Interchange Format. A standard JSON format used by SAST tools to report findings.
- Triage — The process of going through a long list of findings and deciding which to act on first.
- OWASP Top 10 (latest): <https://owasp.org/www-project-top-ten/>
- OWASP SAST overview: <https://owasp.org/www-community/Source_Code_Analysis_Tools>
- CWE Top 25: <https://cwe.mitre.org/top25/>

