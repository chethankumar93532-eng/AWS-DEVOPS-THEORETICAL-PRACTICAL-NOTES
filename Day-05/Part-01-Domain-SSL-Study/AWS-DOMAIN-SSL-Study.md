# Day-05 Study Notes — Domain, EC2, Route 53 and SSL/TLS

## 1. Domain

A domain provides a human-readable name for reaching the server/application.

In this hands-on:

```text
chethandevops.xyz
```

was the domain used.

## 2. DNS

DNS connects the domain name to the server destination.

The practiced relationship was:

```text
chethandevops.xyz
        ↓
DNS
        ↓
EC2 Public IP
```

## 3. Route 53

Route 53 was used to create the hosted zone and DNS records.

Source flow:

```text
Created hosted zone
        ↓
Created sub records
```

## 4. EC2 Web Server

EC2 was launched to host the application/web server.

Nginx was used as the web server.

```text
EC2
 ↓
Nginx
 ↓
Application
```

## 5. HTTP vs HTTPS

HTTP provides web communication without the TLS protection used by HTTPS.

HTTPS adds SSL/TLS protection to the connection.

The Day-05 exercise focused on understanding the relationship between:

```text
Domain
+
DNS
+
SSL/TLS
+
HTTPS
```

## 6. AWS Certificate Manager

AWS Certificate Manager (ACM) was used to request an SSL/TLS certificate.

DNS validation was selected.

The certificate validation process was:

```text
ACM Certificate Request
        ↓
DNS Validation
        ↓
Validation Record
        ↓
DNS Ownership Verification
        ↓
Certificate Issued
```

## 7. Pending Validation

The actual certificate state recorded in the source was:

```text
Pending validation
```

This means the certificate was waiting for the DNS validation process to complete.

The source states that after DNS validation is completed, the certificate should move to:

```text
Issued
```

## 8. Certbot

The source also records the use of a Certbot command to attach a third-party SSL certificate.

This is kept as an observed hands-on step rather than expanding beyond what the source documents.

## 9. End-to-End Mental Model

Remember:

```text
USER
 ↓
DOMAIN
 ↓
DNS / ROUTE 53
 ↓
EC2
 ↓
NGINX
 ↓
SSL/TLS
 ↓
HTTPS
 ↓
APPLICATION
```

## 10. Day-05 Key Takeaway

The important connection is:

> DNS answers where the domain should go; the web server serves the application; SSL/TLS protects the HTTPS connection; ACM manages the AWS certificate lifecycle.
