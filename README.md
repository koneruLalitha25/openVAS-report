# openVAS-report

## 📌 Project Overview

The objective was to identify exposed services, collect information about the target system, analyze SSL/TLS configuration, review HTTP security headers, and identify potential security weaknesses that could increase the attack surface.


## Key Objectives

* Perform a vulnerability assessment using OpenVAS/Greenbone.
* Identify exposed network services and ports.
* Detect the web server and operating system information.
* Analyze HTTP and HTTPS configurations.
* Review SSL/TLS protocols and cipher suites.
* Identify missing or insecure security headers.
* Analyze `robots.txt` exposure.
* Document findings and provide security recommendations.

---

## 🛠️ Tools Used

| Tool                    | Purpose                         |
| ----------------------- | ------------------------------- |
| **OpenVAS / Greenbone** | Vulnerability scanning          |
| **Nmap**                | Network and service enumeration |
| **Web Browser**         | Web server verification         |
| **Linux/Greenbone OS**  | Scanning environment            |

---

## 🌐 Target Information

**Target IP:** `192.168.1.71`

**Scan Date:** September 2, 2026

### Detected Services

| Port    | Protocol | Service                |
| ------- | -------- | ---------------------- |
| 80/tcp  | HTTP     | Nginx Web Server       |
| 443/tcp | HTTPS    | Nginx / TLS Web Server |

The scan identified Nginx running on both ports 80 and 443.

---

## 📊 Scan Summary

The OpenVAS report contained **31 results**, all classified as **Log** results with a CVSS of 0.0 in this report. No Critical, High, Medium, or Low results were shown in the filtered report.

| Severity          | Findings |
| ----------------- | -------: |
| Critical          |        0 |
| High              |        0 |
| Medium            |        0 |
| Low               |        0 |
| Informational/Log |       31 |

> Note: Informational/log findings can still provide useful information about the target's configuration and attack surface.

---

## 🔎 Key Findings

### 1. Nginx Server Detected

The scanner identified **Nginx** as the HTTP server on ports 80 and 443. The exact Nginx version was not identified.

**Security consideration:**
Server information can assist attackers during reconnaissance. Keeping unnecessary server details hidden can reduce information disclosure.

---

### 2. HTTP Response Handling

The server returned unexpected responses for non-existent URLs. On HTTPS, the scanner observed a `200` response for non-existent files/URLs.

**Security consideration:**

Incorrect handling of nonexistent resources can interfere with security scanning and may make application behavior harder to analyze.

**Recommendation:**

* Configure appropriate `404 Not Found` responses.
* Review Nginx rewrite and routing rules.
* Verify custom error-page configuration.

---

### 3. Self-Signed / Untrusted TLS Certificate

The HTTPS service was detected using a **self-signed certificate**, and the certificate failed verification against the system trust store.

**Security consideration:**

Self-signed certificates may cause trust warnings and are generally unsuitable for production public-facing services unless properly managed within a trusted internal PKI.

**Recommendation:**

Use a certificate issued by a trusted Certificate Authority for production systems.

---

### 4. HSTS Missing

The scan reported that **HTTP Strict Transport Security (HSTS)** was not enforced.

**Security consideration:**

Without HSTS, browsers are not instructed to exclusively use HTTPS for future connections.

**Recommendation:**

Configure the following header after confirming HTTPS is correctly deployed:

```http
Strict-Transport-Security: max-age=31536000; includeSubDomains
```

---

### 5. HTTP Security Headers

Several security headers were detected, including:

* `Content-Security-Policy`
* `X-Content-Type-Options`
* `X-Frame-Options`
* `X-XSS-Protection`

However, OpenVAS also identified several missing headers, including:

* `Strict-Transport-Security`
* `Referrer-Policy`
* `Permissions-Policy`
* `Cross-Origin-Opener-Policy`
* `Cross-Origin-Resource-Policy`

**Recommendation:**

Review the application's security-header policy and implement appropriate modern security headers based on application requirements.

---

### 6. TLS Configuration

The server supported:

* TLS 1.2
* TLS 1.3

The scan identified several TLS 1.3 cipher suites supporting Perfect Forward Secrecy, including:

```text
TLS_AES_128_GCM_SHA256
TLS_AES_256_GCM_SHA384
TLS_CHACHA20_POLY1305_SHA256
```

The scan also reported RSA-based TLS 1.2 cipher suites categorized as medium/non-weak rather than strong.

**Recommendation:**

Review the TLS configuration and prioritize modern, strong cipher suites while disabling unnecessary legacy configurations.

---

## 🤖 robots.txt Analysis

The scanner discovered:

```text
User-agent: *
Disallow: /
```

`robots.txt` should **not** be treated as an access-control mechanism. Sensitive resources should be protected using authentication and authorization controls rather than relying on `robots.txt`.

---

## 🔐 Security Recommendations

1. Replace the self-signed/untrusted certificate with a properly trusted certificate where appropriate.
2. Enable HSTS after confirming HTTPS configuration.
3. Review and strengthen HTTP security headers.
4. Configure correct `404` responses for nonexistent resources.
5. Minimize unnecessary server/banner information disclosure.
6. Review TLS 1.2 cipher suites and prefer modern secure configurations.
7. Review the contents of `robots.txt` and ensure sensitive resources are protected through proper access controls.
8. Regularly perform vulnerability scans and keep the server/software updated.

---

## 📚 What I Learned

Through this project, I learned how to perform a vulnerability assessment using OpenVAS/Greenbone and interpret automated scan results. I gained practical experience in identifying exposed services, analyzing Nginx and SSL/TLS configurations, reviewing HTTP security headers, and understanding information-disclosure risks. I also learned that informational findings can provide valuable insight into a system's attack surface even when no high-severity vulnerabilities are reported.

---

## ⚖️ Disclaimer

This project was conducted in an authorized laboratory environment for educational purposes. Vulnerability scanning should only be performed against systems for which you have explicit permission to test.

---
