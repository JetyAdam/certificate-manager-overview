# Certificate Management System | Internal Enterprise Application

> **Disclaimer:** This project was developed for internal use at Red Hat. The source code is proprietary and cannot be shared publicly. This document provides a detailed overview of the project's architecture, features, and the technical challenges involved.

## Project Overview

This enterprise-grade system was designed to automate the management and monitoring of thousands of SSL/TLS certificates across internal infrastructure. As one of two developers on the project, I helped build a full-stack solution that provides teams with automated expiration tracking, tiered notifications, and a comprehensive audit history to ensure security and compliance.

## Key Features

* ** Automated Expiration Notifications:** A multi-tiered notification system sends warning, urgent, and critical alerts via email as certificates approach expiration.
* ** Multiple Input Methods:** Supports adding certificates via hostname discovery, file upload, or raw Base64 text, accommodating various workflows.
* ** Advanced Filtering & Tagging:** A robust UI allows users to filter certificates by expiration date, hostname, and other metadata. A flexible tagging system provides powerful organizational capabilities.
* ** Comprehensive Audit Trail:** Every action (create, update, delete) is logged with user and timestamp details, providing a full audit trail for compliance and troubleshooting.
* ** Background Job Processing:** A reliable, database-backed job queue handles all background tasks, including daily certificate validation checks and scheduled email notifications.

## Tech Stack

This project was built using a modern, type-safe stack to ensure reliability and maintainability.

| Category          | Technologies                                                                                             |
| ----------------- | -------------------------------------------------------------------------------------------------------- |
| **Full-Stack Framework** | Wasp (a declarative DSL for React/Node.js apps)                                                   |
| **Frontend** | React, TypeScript, Chakra UI                                                                                  |
| **Backend** | Node.js, Express                                                                                               |
| **Database** | PostgreSQL, Prisma ORM                                                                                        |
| **Job Scheduler** | PgBoss (PostgreSQL-based job queue)                                                                      |
| **DevOps & Deployment** | Docker, Kubernetes / OpenShift                                                                     |

## My Role & Contributions

In this project, I was one of two core developers responsible for the end-to-end implementation. My key contributions included:

* Architecting and implementing the backend REST API in Node.js, including all business logic for certificate processing and data management.
* Designing the PostgreSQL database schema and writing all migrations using Prisma.
* Developing key frontend components in React and TypeScript, including the main dashboard, filtering logic, and modal forms.
* Implementing the background worker and job scheduling system with PgBoss for automated validation and email notifications.
* Containerizing the application with Docker and creating the deployment configurations for OpenShift.

## Technical Deep Dive: Challenges & Solutions

We faced several interesting technical challenges while building this application for an enterprise environment.

#### 1. Secure & Reliable Certificate Validation

* **Challenge:** How to safely interact with the operating system (to use OpenSSL for validation) from a Node.js server, without risking crashes from invalid hostnames or malformed certificates.
* **Solution:** I implemented a robust validation module that calls the `openssl` command in a sandboxed child process. The PEM output is then safely parsed using the `node-forge` library. Comprehensive error handling ensures that any failure marks a certificate as "invalid" in the database rather than crashing the background worker.

#### 2. Multi-Container Orchestration

* **Challenge:** The application consists of three distinct processes (frontend server, backend API, and database) that need to be deployed and communicate seamlessly within a Kubernetes/OpenShift environment.
* **Solution:** We designed a deployment model using a single pod containing three separate containers. This allowed the services to communicate over `localhost`, simplifying the network configuration. Persistent volumes were used for the database to ensure data durability, and HTTPS was enabled by mounting TLS certificates managed by OpenShift secrets.

#### 3. Enterprise Deployment & Routing

* **Challenge:** The application needed to run behind a corporate reverse proxy, which required all application routes to be prefixed with a specific path (e.g., `/my-app/`).
* **Solution:** I configured the build process to be environment-aware. Using environment variables, the React router's `basename` and the Express server's root path are dynamically set at build time, allowing the same codebase to be deployed to different environments (dev, stage, prod) with different URL prefixes without code changes.
