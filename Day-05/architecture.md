# Day-05 Architecture — Domain, EC2 Server Deployment, Route 53 and SSL/TLS

## End-to-End Architecture

```text
                         👤 USER
                           |
                           | HTTPS request
                           v
                 +----------------------+
                 |      GoDaddy Domain  |
                 |  chethandevops.xyz   |
                 +----------+-----------+
                            |
                            | DNS
                            v
                 +----------------------+
                 |      Route 53        |
                 |    Hosted Zone       |
                 |                      |
                 | DNS records           |
                 +----------+-----------+
                            |
                            | Resolves to
                            v
                 +----------------------+
                 |      AWS / VPC       |
                 |                      |
                 |   +--------------+   |
                 |   |     EC2      |   |
                 |   |   Web Server |   |
                 |   |    Nginx     |   |
                 |   +------+-------+   |
                 +----------+-----------+
                            |
                            v
                    SSL/TLS / HTTPS
                            |
                            v
                       APPLICATION
```

## SSL/TLS Validation Flow

```text
ACM Certificate Request
          |
          v
     DNS Validation
          |
          v
 Add required DNS validation record
          |
          v
 ACM checks DNS ownership
          |
     +----+----+
     |         |
Pending     Validated
Validation     |
               v
            Issued
```

## Actual Day-05 Status

- EC2 server: Launched
- GoDaddy domain: Configured
- DNS resolution: Configured/Tested
- Route 53 hosted zone: Created
- DNS records: Created
- Nginx/web server: Used in the flow
- ACM certificate: **Pending validation**
- HTTPS completion: Pending certificate validation

> Note: The source also records an attached third-party SSL certificate using a Certbot command, while the AWS Certificate Manager certificate remained pending validation.
