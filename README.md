# cognizant-hackthon
A Banking Loan Application Processing System built using MuleSoft Anypoint Studio, integrating REST APIs, MySQL, and ActiveMQ to automate loan application intake, validation, storage, and asynchronous processing.
# Banking Loan Application Processing System

A MuleSoft-based Banking Loan Application Processing System developed as part of the **Cognizant Hackathon**. The project aims to automate loan application intake, validation, verification, credit scoring, decision-making, and status tracking through an integrated, event-driven architecture.

## Project Overview

The system uses REST APIs, database integration, and asynchronous messaging to streamline the loan application lifecycle.

It is designed using a modular architecture, where each module handles a specific stage of loan processing and communicates with other modules through messaging queues and shared data.

## Technology Stack

- **Integration Platform:** MuleSoft Anypoint Studio 7.28
- **Runtime:** Mule Runtime 4.12.2
- **Programming Language:** Java 17
- **API Specification:** RAML
- **Database:** MySQL
- **Messaging System:** ActiveMQ Classic 5.19.12
- **API Testing:** Postman
- **Version Control:** Git and GitHub

## Project Modules

| Module | Responsibility |
|---|---|
| Module 1 – API & Application Intake | Receives applications, validates requests, generates application IDs, stores application details, and publishes messages |
| Module 2 – Verification | Handles applicant and document verification |
| Module 3 – Credit Scoring | Evaluates applicant financial information and calculates credit scores |
| Module 4 – Loan Decision | Processes scoring and verification results to determine loan decisions |
| Module 5 – Status & Notification | Updates application status and handles notifications |

*Note: Module descriptions represent the planned overall project workflow. Individual modules are developed and integrated separately.*

## Module 1 – API & Application Intake

The first module is implemented using MuleSoft Anypoint Studio.

### Features Implemented

- REST API to receive loan applications.
- Request validation, including document count validation.
- Duplicate request detection using the `X-Request-Id` header.
- Unique application ID generation using UUID.
- Storage of application details in MySQL.
- Publishing application messages to ActiveMQ.
- GET API to retrieve application status.
- Appropriate HTTP responses for successful, invalid, duplicate, and unavailable applications.

### Application Workflow

1. Client submits a loan application through the POST API.
2. MuleSoft receives and validates the request.
3. The system checks whether the request ID already exists.
4. A unique application ID is generated for a new application.
5. Application details are inserted into MySQL with the initial status `RECEIVED`.
6. Application details are published to the ActiveMQ verification queue.
7. The API returns HTTP 202 Accepted.
8. The GET API allows clients to retrieve application status.

## API Endpoints

Base URL:

`http://localhost:8081/api/v1`

| Method | Endpoint | Description |
|---|---|---|
| POST | `/loan-applications` | Submit a new loan application |
| GET | `/loan-applications/{applicationId}` | Retrieve application details and status |

### Required POST Header

`X-Request-Id: REQ-1001`

The request ID is used to identify duplicate submissions.

### HTTP Response Codes

| Status Code | Description |
|---|---|
| 202 | Application accepted for processing |
| 200 | Application details retrieved successfully |
| 400 | Invalid request |
| 404 | Application not found |
| 422 | Business validation failed |
| 500 | Internal processing error |

**Note:** HTTP 202 indicates that the application has been accepted for processing. It does not mean the loan has been approved.

## Database Configuration

Database name: `loan_processing_db`

Table name: `loan_applications`

The table stores:

- Application identification details
- Applicant information
- Employment details
- Loan product information
- Financial information
- Document manifest
- Application status
- Application received timestamp

The database creation script is available at:

`database/database-schema.sql`

### Database Setup

1. Open MySQL Workbench or MySQL Command Line.
2. Execute the SQL script located in the `database` folder.
3. Configure the MySQL connection in Anypoint Studio.
4. Verify the database connection before running the Mule application.

## ActiveMQ Configuration

ActiveMQ is used for asynchronous communication between processing modules.

**Broker URL (local development):**

`tcp://localhost:61616`

**Queue Name:**

`loan.verification.queue`

### Message Processing

After successful validation and database insertion, Module 1 publishes the application details to the verification queue.

The message contains:

- Application ID
- Request ID
- Applicant details
- Loan product details
- Financial information
- Document manifest

The verification module can consume this message using a JMS Listener.

For distributed development, configure a shared ActiveMQ broker and update the connection URL accordingly.

## Project Structure

```text
Banking-Loan-Processing/
│
├── loan-intake-api/
│   ├── src/
│   │   └── main/
│   │       ├── mule/
│   │       └── resources/
│   ├── pom.xml
│   └── mule-artifact.json
│
├── api-specification/
│   └── RAML files
│
├── database/
│   └── database-schema.sql
│
├── sample-requests/
│   └── sample-request.json
│
└── README.md
```

## Prerequisites

Before running the application, install:

1. Anypoint Studio 7.28.
2. Java JDK 17.
3. Mule Runtime 4.12.2.
4. MySQL Server.
5. ActiveMQ Classic 5.19.12.
6. Postman for API testing.

## Running the Application

1. Clone the repository:

   ```bash
   git clone <repository-url>
   ```

2. Open Anypoint Studio.

3. Import the `loan-intake-api` project.

4. Configure the MySQL database connection.

5. Start the MySQL server.

6. Start the ActiveMQ broker.

7. Verify the JMS and Database connector configurations.

8. Run the Mule application in Anypoint Studio.

9. Test the REST endpoints using Postman.

## Testing & Validation

The following Module 1 scenarios have been tested:

| Test Scenario | Expected Result | Result |
|---|---|---|
| Valid loan application submission | HTTP 202 | Passed |
| Invalid document count | HTTP 422 | Passed |
| Duplicate request submission | Existing application returned | Passed |
| Database insertion | Application record created | Passed |
| JMS publishing | Message published to queue | Passed |
| GET with valid application ID | HTTP 200 | Passed |
| GET with invalid application ID | HTTP 404 | Passed |
| JMS message inspection | Application data visible | Passed |

## Known Limitations

- Automatic retry and recovery for failures between MySQL insertion and JMS publishing are not implemented.
- The current ActiveMQ configuration uses a local development broker.
- Production deployment requires secure credential management, distributed broker configuration, and additional failure-handling mechanisms.

## Future Enhancements

- Implement transactional messaging or the Outbox Pattern.
- Add automated retry and error recovery.
- Integrate all processing modules.
- Implement enhanced security and authentication.
- Add monitoring and centralized logging.
- Deploy the complete application to a cloud environment.

## Contributors

Developed collaboratively as part of the Cognizant Hackathon.

---

**Project:** Banking Loan Application Processing System  
**Integration Platform:** MuleSoft Anypoint Studio  
**Focus:** API Integration | Database Management | Asynchronous Messaging | Loan Processing Automation
