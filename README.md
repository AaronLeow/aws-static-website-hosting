# 🌐 Static Website Hosting on AWS

A production-grade static website deployed on AWS using a secure, globally distributed architecture — featuring CloudFront CDN, HTTPS enforcement, DNS management, and WAF protection against common web exploits.

Results: https://aaronleow.me/

---

## 📐 Architecture

```
User Request
     │
     ▼
Amazon Route 53       ← DNS resolution (custom domain)
     │
     ▼
AWS WAF               ← Web Application Firewall (SQLi, XSS protection)
     │
     ▼
Amazon CloudFront     ← Global CDN (edge caching, HTTPS termination)
     │
     ▼
AWS ACM               ← SSL/TLS Certificate (HTTPS enforcement)
     │
     ▼
Amazon S3             ← Static website origin (private bucket + OAC)
```

> Direct public access to S3 is blocked. All traffic is routed through CloudFront using Origin Access Control (OAC), enforcing HTTPS and WAF inspection at the edge.

---

## ☁️ AWS Services Used

| Service | Purpose |
|---|---|
| Amazon S3 | Static file hosting (HTML, CSS, JS) |
| Amazon CloudFront | Global content delivery network (CDN) |
| Amazon Route 53 | DNS management and custom domain routing |
| AWS Certificate Manager (ACM) | SSL/TLS certificate provisioning and renewal |
| AWS WAF | Web Application Firewall — protection against SQLi, XSS, and common exploits |

---

## 🔐 Security Design Decisions

- **S3 bucket is private** — direct public access is disabled
- **Origin Access Control (OAC)** is used instead of the legacy OAI, ensuring only CloudFront can fetch from S3
- **HTTPS enforced** — HTTP requests are redirected to HTTPS at the CloudFront level
- **WAF rules applied** — AWS Managed Rules block common web attack vectors (SQLi, XSS, bad bots)
- **Least-privilege S3 bucket policy** — only allows `s3:GetObject` from the specific CloudFront distribution

---

## 🚀 Deployment Steps

1. **Create S3 bucket** — disable public access, enable static website hosting
2. **Upload website files** — HTML, CSS, JS assets
3. **Request ACM certificate** — for custom domain (must be in `us-east-1` for CloudFront)
4. **Create CloudFront distribution** — set S3 as origin, configure OAC, enforce HTTPS
5. **Attach WAF Web ACL** — associate AWS Managed Rules to the CloudFront distribution
6. **Configure Route 53** — create A record (Alias) pointing to CloudFront distribution

---

## 🧱 Challenges & What I Learned

**Challenge 1: S3 bucket policy not working with CloudFront**
Initially configured S3 for public access, then switched to OAC. Had to update the bucket policy to reference the correct CloudFront distribution ARN. Learned the difference between OAI (legacy) and OAC (current best practice).

**Challenge 2: ACM certificate region**
ACM certificate must be created in `us-east-1` (North Virginia) for CloudFront to use it, even if the S3 bucket is in a different region. This was not obvious and caused initial deployment failure.

**Challenge 3: WAF association**
WAF Web ACL must also be created in `us-east-1` to be attachable to a CloudFront distribution (same regional requirement as ACM).

---

## 💡 Key Concepts Demonstrated

- **CDN fundamentals** — how edge caching reduces latency and origin load
- **DNS routing** — how Route 53 Alias records differ from standard CNAME
- **Certificate management** — ACM auto-renewal vs manual certificate management
- **Defence-in-depth** — layered security (WAF → HTTPS → private S3) rather than relying on a single control
- **Least-privilege access** — S3 bucket policy scoped to a single CloudFront distribution

---

---

*Built as part of my AWS cloud portfolio while completing the AWS re/Start program (2025–2026).*
