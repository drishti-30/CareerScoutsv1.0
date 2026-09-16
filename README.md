# CareerScout v 1.0
CareerScout

An automated, explainable job discovery and matching platform built with Java and Spring Boot.

CareerScout continuously discovers relevant jobs and internships from permitted original company career sources and ATS platforms, analyzes their requirements, compares them against a user's resume and preferences, and delivers actionable job recommendations.

The system is designed as a production-oriented backend project with an emphasis on automation, explainable matching, clean architecture, security, and scalability.

Overview

Searching for jobs manually across company career pages, ATS platforms, and multiple job boards is time-consuming. CareerScout automates the discovery and analysis process.

The system:

1. Accepts and processes a user's resume.
2. Builds a structured candidate profile.
3. Stores the user's skills, education, experience, and preferences.
4. Discovers newly posted jobs from permitted sources.
5. Normalizes and deduplicates job data.
6. Extracts structured requirements from job descriptions.
7. Compares jobs against the candidate profile.
8. Identifies matched and missing requirements.
9. Generates an explainable match result.
10. Provides an application checklist and original application source.
11. Sends relevant opportunities through email.
12. Runs automatically on a scheduled basis.

CareerScout does not automatically submit applications. The final application decision and submission remain under the user's control.

Core Workflow

                    USER RESUME
                         |
                         v
                 Resume Processing
                         |
                         v
               Structured Candidate
                     Profile
                         |
                         v
                 User Preferences
                         |
                         v
               Job Discovery Engine
                         |
          +--------------+--------------+
          |              |              |
          v              v              v
       ATS Source     Career Page      API/Feed
          |              |              |
          +--------------+--------------+
                         |
                         v
                  Job Normalization
                         |
                         v
                   Deduplication
                         |
                         v
                Requirement Extraction
                         |
                         v
                  Matching Engine
                         |
                         v
               Explainable Job Match
                         |
              +----------+----------+
              |                     |
              v                     v
        Application            Skill Gap
        Requirements             Analysis
              |                     |
              +----------+----------+
                         |
                         v
                  Relevant Jobs
                         |
                         v
                  Email Notification
                         |
                         v
             Original Application Source
                         |
                         v
                   User Decision

Key Features

Resume Intelligence

CareerScout processes supported resume documents and extracts structured information such as:

- Technical skills
- Programming languages
- Frameworks
- Databases
- Education
- Experience
- Projects
- Certifications

Extracted information can be reviewed and corrected by the user before being used by the matching engine.

Automated Job Discovery

The job discovery engine is designed to work with permitted sources such as:

- Company career platforms
- Company ATS platforms
- Official APIs
- Public feeds
- Other permitted job sources

The system uses a connector-based architecture so additional sources can be integrated without changing the core job-processing logic.

JobSourceConnector
        |
        +-- ATS Connector
        +-- API Connector
        +-- Feed Connector
        +-- Career Page Connector
        +-- Future Connectors

CareerScout does not bypass authentication, CAPTCHA, anti-bot mechanisms, access controls, or source restrictions.

Job Normalization

Different sources can represent the same information differently.

CareerScout normalizes:

- Job titles
- Company names
- Skills
- Locations
- Employment types
- Work modes
- Experience levels
- Application URLs
- Dates

This creates a consistent internal representation of job data.

Duplicate Detection

The system prevents the same opportunity from appearing multiple times.

Deduplication can use:

Source + External Job ID
        |
Canonical Application URL
        |
Company + Job Title + Location
        |
Similarity Checks

Requirement Extraction

Job descriptions are converted into structured requirements.

Example:

Job Description
       |
       v
Text Processing
       |
       v
Requirement Extraction
       |
       +-- Skills
       +-- Experience
       +-- Education
       +-- Eligibility
       +-- Location
       +-- Other Requirements
       |
       v
Required / Preferred Classification

The system distinguishes between required and preferred requirements wherever the source provides enough information to do so.

Explainable Matching

CareerScout does not rely on a simple keyword count.

A match result can contain:

Match Score

Matched Skills
Missing Skills

Matched Requirements
Missing Requirements

Preference Matches

Eligibility Status

Explanation

Example:

Job: Backend Developer

Matched:
- Java
- Spring Boot
- SQL
- REST APIs

Missing:
- Redis

Preferences:
- Backend Development: Match
- Remote: Match
- Full-Time: Match

Eligibility:
- Education requirement satisfied

Explanation:
Strong alignment with the technical requirements, with Redis
being the primary identified skill gap.

The matching engine is initially rule-based and explainable. Future versions may use semantic or AI-assisted techniques for tasks such as skill normalization and requirement extraction.

AI is not intended to be the source of truth for factual job information such as application URLs, job identifiers, dates, or employer-provided requirements.

Application Intelligence

For every relevant job, CareerScout can identify important information needed before applying, including:

- Required skills
- Education requirements
- Experience requirements
- Eligibility criteria
- Location requirements
- Employment type
- Work mode
- Other stated requirements

The system also provides the original application source so the user can review the opportunity and apply directly.

Email Notifications

CareerScout can deliver relevant job recommendations through email.

The notification pipeline is:

Job Match
    |
    v
Notification Service
    |
    v
Email Template
    |
    v
Email Provider
    |
    v
User

The email system is designed behind an internal email abstraction so the provider can be changed without affecting the rest of the application.

Technology Stack

Backend

- Java
- Spring Boot
- Spring Web
- Spring Data JPA
- Hibernate
- Spring Security
- Jakarta Bean Validation

Database

- PostgreSQL

Testing

- JUnit
- Mockito
- Spring Boot Test
- Integration Testing

Development

- Maven
- Git
- GitHub
- Docker
- Docker Compose
- OpenAPI / Swagger

Email

- Transactional email provider
- Resend integration planned

Future Technologies

Depending on scale and requirements:

- Redis
- Message queues
- Elasticsearch / OpenSearch
- Object storage
- AI/LLM-assisted processing
- Advanced observability

Architecture

CareerScout initially follows a modular monolith architecture.

                    Spring Boot Application
                            |
        +-------------------+-------------------+
        |                   |                   |
        v                   v                   v
     Authentication      User/Profile        Resume
        |                   |                   |
        +-------------------+-------------------+
                            |
                            v
                    Job Discovery
                            |
                            v
                    Job Processing
                            |
                            v
                    Requirement Engine
                            |
                            v
                    Matching Engine
                            |
             +--------------+--------------+
             |                             |
             v                             v
       Application                    Notification
             |                             |
             +--------------+--------------+
                            |
                            v
                        PostgreSQL

A modular monolith keeps the initial system easier to develop, test, deploy, and maintain while allowing individual components to be separated into services later if scaling requirements justify it.

Project Structure

com.careerscout
|
+-- auth
|   +-- controller
|   +-- service
|   +-- repository
|   +-- entity
|   +-- dto
|   +-- security
|
+-- user
|   +-- controller
|   +-- service
|   +-- repository
|   +-- entity
|   +-- dto
|
+-- resume
|   +-- controller
|   +-- service
|   +-- parser
|   +-- entity
|   +-- dto
|
+-- job
|   +-- controller
|   +-- service
|   +-- repository
|   +-- entity
|   +-- dto
|
+-- discovery
|   +-- connector
|   +-- service
|   +-- scheduler
|
+-- matching
|   +-- service
|   +-- engine
|   +-- dto
|
+-- application
|   +-- controller
|   +-- service
|   +-- repository
|   +-- entity
|
+-- notification
|   +-- service
|   +-- email
|   +-- scheduler
|
+-- common
|   +-- exception
|   +-- response
|   +-- validation
|
+-- config

Main Domain Model

The primary entities are:

User
 |
 +-- Profile
 |
 +-- Resume
 |
 +-- UserSkill
 |       |
 |       +-- Skill
 |
 +-- Application
 |       |
 |       +-- Job
 |
 +-- SavedJob
 |
 +-- JobMatch
         |
         +-- Job

Company
   |
   +-- Job
          |
          +-- JobSkill
          |
          +-- JobRequirement
          |
          +-- JobSource

Main Entities

User

Stores authentication and account information.

Profile

Stores structured candidate information and preferences.

Resume

Stores resume metadata, processing status, and references to stored resume data.

Skill

Stores canonical technical and professional skills.

UserSkill

Represents a relationship between a user and a normalized skill.

Company

Represents an employer.

Job

Stores normalized job information.

JobRequirement

Stores structured requirements extracted from job descriptions.

JobSkill

Associates normalized skills with jobs.

JobSource

Represents the source from which a job was discovered.

JobMatch

Stores the result of comparing a user profile with a job.

Application

Tracks jobs the user has applied to and their application status.

SavedJob

Allows users to save opportunities for later.

API

The backend exposes versioned REST APIs.

/api/v1/auth
/api/v1/users
/api/v1/profile
/api/v1/resumes
/api/v1/jobs
/api/v1/matches
/api/v1/applications
/api/v1/saved-jobs
/api/v1/notifications

Authentication

POST /api/v1/auth/register
POST /api/v1/auth/login

Resume

POST   /api/v1/resumes
GET    /api/v1/resumes
GET    /api/v1/resumes/{id}
DELETE /api/v1/resumes/{id}

Jobs

GET /api/v1/jobs
GET /api/v1/jobs/{id}

Example filters:

GET /api/v1/jobs?location=remote
GET /api/v1/jobs?type=INTERNSHIP
GET /api/v1/jobs?skill=JAVA
GET /api/v1/jobs?page=0&size=20

Matching

GET  /api/v1/matches
GET  /api/v1/matches/{jobId}
POST /api/v1/matches/recalculate

Applications

POST   /api/v1/applications
GET    /api/v1/applications
GET    /api/v1/applications/{id}
PATCH  /api/v1/applications/{id}/status
DELETE /api/v1/applications/{id}

Saved Jobs

POST   /api/v1/saved-jobs/{jobId}
GET    /api/v1/saved-jobs
DELETE /api/v1/saved-jobs/{jobId}

Automation

CareerScout is designed to operate automatically.

Scheduled workflows include:

Job Discovery
     |
     v
Job Processing
     |
     v
Requirement Extraction
     |
     v
Candidate Matching
     |
     v
Notification Generation
     |
     v
Email Delivery

The scheduler allows the system to periodically check configured sources for new opportunities without requiring manual searches.

Security

Security is a core part of the system because resumes and candidate profiles contain personal information.

Planned security measures include:

- Password hashing
- Authentication
- Authorization
- Ownership validation
- Input validation
- Secure file handling
- File type validation
- File size restrictions
- Protected API endpoints
- Environment-based secrets
- Secure database credentials
- No sensitive credentials in source control

Sensitive values such as passwords, tokens, and API keys must never be committed to GitHub.

Reliability

External job sources can fail independently.

CareerScout therefore treats source connectors independently:

Source A → SUCCESS
Source B → SUCCESS
Source C → FAILED
Source D → PARTIAL

A failure in one source should not prevent the remaining sources from being processed.

The system records source synchronization status and failures for troubleshooting.

Responsible Source Access

CareerScout is intended to use job information only through permitted access methods.

The system does not attempt to:

- Bypass CAPTCHA
- Bypass authentication
- Circumvent access controls
- Evade anti-bot protections
- Ignore API limits
- Violate source terms
- Automatically submit applications without user control

Connectors should respect applicable source policies, rate limits, and technical restrictions.

Testing Strategy

Testing is performed at multiple levels.

Unit Tests

Focused on:

- Matching rules
- Skill normalization
- Requirement extraction
- Validators
- Services
- Business logic

Integration Tests

Focused on:

- REST APIs
- PostgreSQL
- Authentication
- Repository operations
- Job ingestion
- Matching workflows

End-to-End Testing

The complete workflow will eventually be tested:

Register
   |
Upload Resume
   |
Build Profile
   |
Discover Jobs
   |
Process Requirements
   |
Calculate Match
   |
Generate Recommendation
   |
Send Email

Configuration

Environment variables are used for sensitive configuration.

Example:

DATABASE_URL
DATABASE_USERNAME
DATABASE_PASSWORD

JWT_SECRET

EMAIL_API_KEY

Secrets should be supplied through the environment or deployment secret manager rather than committed to the repository.

Local Development

Prerequisites

Install:

- Java
- Maven
- PostgreSQL
- Git
- Docker

Clone

git clone <repository-url>
cd CareerScout

Configure Environment

Create the required environment variables for the local environment.

Start PostgreSQL

Using Docker Compose:

docker compose up -d postgres

Run the Application

./mvnw spring-boot:run

On Windows:

mvnw.cmd spring-boot:run

The exact commands may change as the project evolves.

Development Roadmap

Phase 1 — Foundation

- Spring Boot project
- Maven configuration
- PostgreSQL connection
- Base package structure
- User entity
- Basic REST APIs
- Exception handling
- Validation

Phase 2 — Authentication

- Registration
- Login
- Password hashing
- Authentication
- Authorization
- User ownership checks

Phase 3 — Profile and Resume

- Resume upload
- File validation
- Resume text extraction
- Section detection
- Skill extraction
- Education extraction
- Experience extraction
- Candidate profile confirmation

Phase 4 — Job Discovery

- Job source model
- Connector abstraction
- Initial source connectors
- Scheduled discovery
- Job normalization
- Duplicate detection

Phase 5 — Requirement Engine

- Job description processing
- Skill extraction
- Required/preferred classification
- Experience extraction
- Education extraction
- Eligibility extraction
- Skill normalization

Phase 6 — Matching Engine

- Candidate-job comparison
- Required requirement evaluation
- Preferred requirement evaluation
- Preference matching
- Skill gap analysis
- Explainable match results

Phase 7 — Automation and Email

- Scheduled matching
- Notification preferences
- Email templates
- Email provider integration
- Email delivery logging

Phase 8 — Engineering Quality

- Integration tests
- API documentation
- Logging
- Health checks
- Metrics
- Error monitoring
- Performance improvements

Phase 9 — Deployment

- Dockerization
- CI/CD
- Production database
- Environment configuration
- Monitoring
- Deployment

Future Improvements

Potential future improvements include:

- Semantic skill matching
- AI-assisted requirement extraction
- More ATS connectors
- Advanced search
- Personalized job ranking based on user-defined preferences
- Resume version comparison
- Application analytics
- Redis caching
- Message queues
- Elasticsearch/OpenSearch
- Object storage
- Distributed processing
- Multi-user scaling
- Advanced observability

These features will be introduced only when they solve an actual product or engineering requirement.

Engineering Principles

CareerScout follows several core principles:

Explainability Over Black-Box Decisions

The system should explain why a job matched rather than returning only a number.

Source Accuracy

Original employer and permitted ATS sources are preferred for job information and application destinations.

User Control

The system recommends opportunities but does not make the final application decision.

Modular Design

Each major domain has a clear responsibility and can evolve independently.

Security by Design

Candidate information and credentials are treated as sensitive from the beginning.

Scalability Without Premature Complexity

The initial implementation uses a modular monolith. More complex infrastructure is introduced only when justified.

Testable Business Logic

Core matching and processing logic should remain independently testable.

Project Status

CareerScout is under active development.

Current development focus:

Java Foundations
        |
        v
Object-Oriented Programming
        |
        v
Spring Boot Foundations
        |
        v
Database Design
        |
        v
Backend Implementation
        |
        v
CareerScout Core Features

License

License information will be added when the project is ready for public distribution.

Author

Developed as a backend engineering project focused on building a practical, production-oriented job discovery and matching system using Java and Spring Boot.
