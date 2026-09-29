# Day-05 Hands-On — Domain, EC2 Server Deployment and SSL/TLS

## Objective

Configure a domain for an AWS EC2 web server, configure DNS through Route 53, use Nginx as the web server, and work through SSL/TLS certificate configuration.

## 1. EC2 Server Launch

The Day-05 activity started with an EC2 instance to host the application/web server.

Tasks performed:

- Launched an EC2 instance.
- Configured the required network and security settings.
- Verified connectivity to the server.

## 2. GoDaddy Domain Configuration

A domain was purchased from GoDaddy.

Domain used in the source:

```text
chethandevops.xyz
```

The domain DNS was configured to point toward the AWS server, and domain resolution was tested.

## 3. Route 53 Configuration

A Route 53 hosted zone was created.

The source records:

```text
Created hosted zone
        ↓
Created sub records
```

The DNS path practiced was:

```text
Domain
  ↓
GoDaddy DNS / Nameserver configuration
  ↓
Route 53 Hosted Zone
  ↓
DNS Record
  ↓
AWS EC2 Public IP
```

## 4. Nginx / Web Server

Nginx was used as the web server in the deployment flow.

```text
DNS
 ↓
EC2
 ↓
Nginx
 ↓
Application
```

## 5. SSL/TLS Certificate

AWS Certificate Manager (ACM) was used to request a certificate.

Configuration recorded in the source:

- Certificate registered using AWS Certificate Manager.
- DNS validation selected.
- Required DNS validation record started/configured.
- Certificate status was **Pending validation**.

The expected lifecycle after successful DNS validation is:

```text
Pending validation
        ↓
DNS ownership verified
        ↓
Issued
        ↓
HTTPS can be completed with the certificate
```

## 6. Third-Party Certificate / Certbot

The source also records:

```text
Attached 3rd party SSL certificate using certbot command
```

This is kept as part of the original hands-on record.

## 7. HTTPS / Security Understanding

The Day-05 work covered:

- Difference between HTTP and HTTPS.
- SSL/TLS certificates and encrypted communication.
- DNS validation for ACM.
- Connecting the domain, AWS server, and HTTPS/SSL flow.

## 8. End-to-End Flow

```text
User
 ↓
chethandevops.xyz
 ↓
GoDaddy DNS
 ↓
Route 53
 ↓
AWS EC2 Public IP
 ↓
Nginx / Web Server
 ↓
SSL/TLS
 ↓
Secure HTTPS Connection
 ↓
Application
```

## 9. Current Status

| Component | Status |
|---|---|
| EC2 Server | Launched |
| GoDaddy Domain | Configured |
| DNS Resolution | Configured / Tested |
| Route 53 Hosted Zone | Created |
| DNS Records | Created |
| ACM Certificate | Pending Validation |
| HTTPS Completion | Pending certificate validation |

## 10. Commands Used / Practice Commands

The source document does not provide a complete command transcript. The only command explicitly identified is the Certbot command used for the third-party SSL certificate.

```text
certbot
```

No additional command is invented here because it is not present in the source.

## 11. Hands-On Evidence

### Screenshot 01
[Open Screenshot 01](screenshots/01-day05-screenshot.png)

### Screenshot 02
[Open Screenshot 02](screenshots/02-day05-screenshot.png)

### Screenshot 03
[Open Screenshot 03](screenshots/03-day05-screenshot.png)

### Screenshot 04
[Open Screenshot 04](screenshots/04-day05-screenshot.png)

### Screenshot 05
[Open Screenshot 05](screenshots/05-day05-screenshot.png)

### Screenshot 06
[Open Screenshot 06](screenshots/06-day05-screenshot.png)

### Screenshot 07
[Open Screenshot 07](screenshots/07-day05-screenshot.png)

### Screenshot 08
[Open Screenshot 08](screenshots/08-day05-screenshot.png)

### Screenshot 09
[Open Screenshot 09](screenshots/09-day05-screenshot.png)

## Key Learning

The practical flow was:

```text
Domain
 ↓
DNS
 ↓
EC2
 ↓
Nginx
 ↓
SSL/TLS
 ↓
HTTPS
 ↓
Application
```

The main troubleshooting state observed during the exercise was the ACM certificate remaining in **Pending validation** until DNS ownership validation is completed.
