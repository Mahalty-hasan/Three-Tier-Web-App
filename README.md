# AWS Three-Tier Web Application

## Project Overview

This project demonstrates the deployment of a Python Flask web application using a secure AWS three-tier architecture.

The application is divided into three layers:

- **Web Tier** – Nginx running on Amazon EC2
- **Application Tier** – Python Flask running on a private EC2 instance
- **Database Tier** – MySQL running on Amazon RDS

## Architecture

```text
Internet
   |
   v
Public Application Load Balancer
   |
   v
Web EC2 - Nginx
   |
   v
Internal Application Load Balancer
   |
   v
Private EC2 - Flask
   |
   v
Amazon RDS - MySQL
````

### Request Flow

```text
Browser
   ↓
Public ALB
   ↓
Nginx Web Server
   ↓
Internal ALB
   ↓
Flask Application
   ↓
RDS MySQL
```

## AWS Services Used

* Amazon VPC
* Amazon EC2
* Amazon RDS
* Application Load Balancer
* Internet Gateway
* NAT Gateway
* Route Tables
* Security Groups
* Nginx
* Python Flask
* MySQL

## Network Architecture

The project uses a custom VPC:

* **VPC:** `web-app-vpc`
* **CIDR:** `10.0.0.0/16`
* **Region:** Mumbai (`ap-south-1`)

### Subnets

| Tier | Subnet          | CIDR         | Availability Zone |
| ---- | --------------- | ------------ | ----------------- |
| Web  | public-web-az1  | 10.0.1.0/24  | ap-south-1a       |
| Web  | public-web-az2  | 10.0.2.0/24  | ap-south-1b       |
| App  | private-app-az1 | 10.0.11.0/24 | ap-south-1a       |
| App  | private-app-az2 | 10.0.12.0/24 | ap-south-1b       |
| DB   | private-db-az1  | 10.0.21.0/24 | ap-south-1a       |
| DB   | private-db-az2  | 10.0.22.0/24 | ap-south-1b       |

## Web Tier

The Web Tier uses:

* Amazon EC2
* Amazon Linux 2023
* Nginx
* HTTP port 80

Nginx serves the frontend and works as a reverse proxy for API requests.

## Application Tier

The Application Tier uses:

* Python Flask
* Private EC2 instance
* Port 5000

The Flask application provides a health endpoint and handles feedback submissions.

## Database Tier

The Database Tier uses:

* Amazon RDS
* MySQL
* Database: `feedbackdb`
* Port: `3306`
* Public access: Disabled

The `feedback` table stores submitted feedback.

## Security

Security Groups control communication between the tiers.

* Public ALB → HTTP 80 from the Internet
* Web EC2 → HTTP 80
* Web EC2 → Internal ALB on TCP 5000
* Internal ALB → Flask EC2 on TCP 5000
* Flask EC2 → RDS MySQL on TCP 3306

The application and database tiers are not directly exposed to the Internet.

## Load Balancers

### Public ALB

* Name: `web-public-alb`
* Scheme: Internet-facing
* Listener: HTTP 80
* Target Group: `web-tg`

### Internal ALB

* Name: `app-internal-alb`
* Scheme: Internal
* Listener: HTTP 5000
* Target Group: `app-tg`

## Nginx Reverse Proxy

API requests follow this path:

```text
/api/
  ↓
Nginx
  ↓
Internal ALB
  ↓
Flask
```

The Nginx reverse proxy forwards API requests to the internal application load balancer.

## End-to-End Testing

The complete application flow was tested successfully:

```text
Browser
 → Public ALB
 → Nginx
 → Internal ALB
 → Flask
 → RDS MySQL
```

A feedback submission was successfully processed and stored in the RDS MySQL database.

## Public Application

**Public Application URL:**

[http://web-public-alb-547496198.ap-south-1.elb.amazonaws.com/](http://web-public-alb-547496198.ap-south-1.elb.amazonaws.com/)

## Project Evidence

This repository contains:

* AWS architecture diagram
* VPC and subnet screenshots
* Route table and NAT Gateway screenshots
* Security Group screenshots
* EC2 and RDS screenshots
* Application Load Balancer screenshots
* Flask application testing
* Nginx reverse proxy testing
* Public application testing

## Repository Structure

```text
Three-Tier-Web-App/
├── architecture/
│   └── AWS Three-Tier Web Architecture Diagram.png
└── screenshots/
    ├── AWS configuration screenshots
    ├── ALB screenshots
    ├── Flask screenshots
    ├── Nginx screenshots
    └── Application testing screenshots
```

## Project Status

Core AWS three-tier application implementation completed successfully.
