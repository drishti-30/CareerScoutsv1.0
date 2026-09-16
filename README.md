# CareerScoutsv1.0
CareerScout

Technical Requirements & Design Document (TRD)

Version 1.0

---

1. Document Purpose

This document defines the technical architecture and implementation strategy for CareerScout, an automated job discovery, resume analysis, job matching, and email notification platform.

The TRD translates the requirements defined in the PRD into:

- System architecture
- Backend architecture
- Technology stack
- Modules
- Database design
- API design
- Job-source architecture
- Resume-processing pipeline
- Matching engine
- Scheduling
- Email architecture
- Security
- Testing
- Deployment
- Scalability strategy

---

2. System Objective

CareerScout will continuously collect jobs from permitted external sources, convert them into a common internal format, analyze their requirements, compare them with a user's structured profile/resume, and produce explainable recommendations.

The core technical pipeline is:

External Job Sources
        ↓
Source Connectors
        ↓
Job Ingestion
        ↓
Job Normalization
        ↓
Duplicate Detection
        ↓
Requirement Extraction
        ↓
Job Database
        ↓
Matching Engine
        ↑
User Profile + Resume
        ↓
Match Results
        ↓
Notification Service
        ↓
Email

---

3. Architecture Decision

Initial Architecture: Modular Monolith

CareerScout will initially be built as a modular monolith, not microservices.

This is intentional.

The application will have clearly separated modules inside one Spring Boot application.

CareerScout
│
├── Authentication
├── User/Profile
├── Resume
├── Job Discovery
├── Job Processing
├── Matching
├── Application Tracking
├── Notifications
└── Administration

Why?

A modular monolith allows us to:

- Build faster.
- Understand the complete backend.
- Maintain simpler deployment.
- Avoid unnecessary distributed-system complexity.
- Keep module boundaries clear.
- Split modules into microservices later if scale requires it.

---

4. Technology Stack

Backend

Language: Java

Framework: Spring Boot

Build Tool: Maven

---

Web/API

- Spring Web
- RESTful APIs
- JSON

---

Database

Primary database: PostgreSQL

ORM: Spring Data JPA / Hibernate

---

Security

- Spring Security
- Token-based authentication
- Password hashing

---

Validation

- Jakarta Bean Validation

Examples:

@NotBlank
@Email
@Size
@NotNull

---

Testing

- JUnit
- Mockito
- Spring Boot Test
- Integration testing

---

Documentation

OpenAPI/Swagger-compatible API documentation.

---

Version Control

Git + GitHub

---

Containerization

Docker

---

Email

A transactional email provider such as Resend can be integrated for production email delivery.

The email layer will be abstracted so the provider can be changed later.

---

Future Technologies

Potential future additions:

- Redis
- Message queues
- Object storage
- Elasticsearch/OpenSearch
- AI/LLM services

These are not mandatory for MVP.

---

5. High-Level System Architecture

                    ┌─────────────────┐
                    │     Client      │
                    │ Web / Mobile UI │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │   REST API      │
                    │ Spring Web      │
                    └────────┬────────┘
                             │
        ┌────────────────────┼─────────────────────┐
        │                    │                     │
        ▼                    ▼                     ▼
 Authentication       User/Profile            Jobs
        │                    │                     │
        │                    │             ┌───────┴────────┐
        │                    │             │ Job Processing │
        │                    │             └───────┬────────┘
        │                    │                     │
        │                    │                     ▼
        │                    │              Matching Engine
        │                    │                     │
        └────────────────────┼─────────────────────┘
                             │
                             ▼
                       PostgreSQL
                             │
                             ▼
                    Notification Service
                             │
                             ▼
                         Email Provider

---

6. Module Architecture

The backend will be divided into logical modules.

6.1 Authentication Module

Responsibilities:

- Registration
- Login
- Password hashing
- Token generation
- Authentication
- Authorization
- Role management

---

6.2 User Module

Responsibilities:

- User profile
- Preferences
- Account settings
- User status

---

6.3 Resume Module

Responsibilities:

- Resume upload
- Resume storage
- Text extraction
- Structured data extraction
- Resume version management

---

6.4 Job Discovery Module

Responsibilities:

- Source connectors
- Job retrieval
- Scheduled discovery
- Ingestion
- Source tracking

---

6.5 Job Processing Module

Responsibilities:

- Job normalization
- Cleaning
- Requirement extraction
- Deduplication
- Job classification

---

6.6 Matching Module

Responsibilities:

- Resume/job comparison
- Requirement matching
- Preference matching
- Match calculation
- Match explanation
- Skill-gap analysis

---

6.7 Application Module

Responsibilities:

- Saved jobs
- Application tracking
- Application status
- Application checklist

---

6.8 Notification Module

Responsibilities:

- Email generation
- Email preferences
- Digest creation
- Notification scheduling
- Delivery tracking

---

6.9 Administration Module

Responsibilities:

- User management
- Source management
- Job management
- System monitoring

---

7. Package Structure

The initial Spring Boot project should follow a feature-oriented structure.

com.careerscout
│
├── auth
│   ├── controller
│   ├── service
│   ├── repository
│   ├── entity
│   ├── dto
│   └── security
│
├── user
│   ├── controller
│   ├── service
│   ├── repository
│   ├── entity
│   └── dto
│
├── resume
│   ├── controller
│   ├── service
│   ├── parser
│   ├── entity
│   └── dto
│
├── job
│   ├── controller
│   ├── service
│   ├── repository
│   ├── entity
│   └── dto
│
├── discovery
│   ├── connector
│   ├── service
│   └── scheduler
│
├── matching
│   ├── service
│   ├── engine
│   └── dto
│
├── application
│   ├── controller
│   ├── service
│   ├── repository
│   └── entity
│
├── notification
│   ├── service
│   ├── email
│   └── scheduler
│
├── common
│   ├── exception
│   ├── response
│   └── validation
│
└── config

---

8. Database Design

The initial database will use PostgreSQL.

Main entities:

User
Profile
Resume
Skill
UserSkill
Company
Job
JobSkill
JobRequirement
JobSource
JobMatch
Application
SavedJob
NotificationPreference
EmailLog

---

9. Entity Relationships

High-level relationship:

USER
 │
 ├────────── PROFILE
 │
 ├────────── RESUME
 │
 ├────────── USER_SKILL
 │                 │
 │                 ▼
 │               SKILL
 │
 ├────────── APPLICATION
 │                 │
 │                 ▼
 │                JOB
 │
 ├────────── SAVED_JOB
 │
 └────────── JOB_MATCH
                   │
                   ▼
                  JOB


COMPANY
   │
   └────────── JOB
                 │
                 ├── JOB_SKILL
                 └── JOB_REQUIREMENT

JOB
 │
 └── JOB_SOURCE

---

10. User Entity

Conceptual fields:

User
----
id
name
email
passwordHash
role
status
createdAt
updatedAt

Roles:

CANDIDATE
ADMIN

A recruiter/company role can be introduced if the product later expands into a two-sided platform.

---

11. Profile Entity

Profile
-------
id
userId
headline
location
educationSummary
experienceSummary
createdAt
updatedAt

Detailed skills and experience should be represented using appropriate related entities rather than storing everything as one large text field.

---

12. Skill Entity

Skill
-----
id
name
normalizedName
category

Example:

Java
Spring Boot
SQL
PostgreSQL
Docker
AWS
Git

---

13. UserSkill Entity

This represents the relationship between a user and a skill.

UserSkill
---------
id
userId
skillId
proficiency
source

Possible source:

RESUME
USER_CONFIRMED
MANUAL

This allows us to distinguish automatically extracted skills from skills confirmed by the user.

---

14. Resume Entity

Resume
------
id
userId
fileName
fileType
storageReference
parsedText
version
status
createdAt

Possible status:

UPLOADED
PROCESSING
PROCESSED
FAILED

---

15. Company Entity

Company
-------
id
name
website
description
industry
createdAt
updatedAt

---

16. Job Entity

Job
---
id
externalJobId
companyId
sourceId
title
description
location
workMode
employmentType
experienceLevel
postedAt
deadline
applicationUrl
status
createdAt
updatedAt

---

17. Job Requirement Entity

JobRequirement
--------------
id
jobId
type
name
value
importance
sourceText

Requirement type could include:

SKILL
EDUCATION
EXPERIENCE
ELIGIBILITY
LOCATION
OTHER

Importance:

REQUIRED
PREFERRED

---

18. Job Skill Entity

JobSkill
--------
id
jobId
skillId
required

This allows efficient comparison between:

User Skills
      ↕
Job Skills

---

19. Job Source Entity

JobSource
---------
id
name
sourceType
baseUrl
active
lastSuccessfulSync
createdAt

Examples of source types:

ATS
API
FEED
CAREER_PAGE

---

20. Source Connector Architecture

This is one of the most important technical decisions.

We will define an abstraction such as:

JobSourceConnector
        │
        ├── Connector A
        ├── Connector B
        ├── Connector C
        └── Future Connector

Conceptually:

interface JobSourceConnector {

    fetchJobs()

    fetchJobDetails()

    supports(source)

}

Each connector converts external source data into our internal job model.

This means adding another permitted source should not require rewriting the matching engine.

---

21. Job Ingestion Pipeline

Scheduler
   ↓
Select Active Sources
   ↓
Connector
   ↓
Fetch Jobs
   ↓
Validate Data
   ↓
Normalize Data
   ↓
Check Duplicate
   ↓
Store Job
   ↓
Extract Requirements
   ↓
Ready for Matching

---

22. Job Deduplication

Potential duplicate identifiers:

1. Source + external job ID
2. Canonical application URL
3. Company + normalized title + location
4. Additional similarity checks

The system should prefer deterministic identifiers before using more expensive similarity logic.

---

23. Resume Processing Pipeline

Resume Upload
      ↓
File Validation
      ↓
Text Extraction
      ↓
Text Cleaning
      ↓
Section Detection
      ↓
Skill Extraction
      ↓
Education Extraction
      ↓
Experience Extraction
      ↓
Structured Profile
      ↓
User Confirmation

---

24. Resume Parser Design

The parser should have separate responsibilities.

ResumeParser
     │
     ├── TextExtractor
     ├── SectionParser
     ├── SkillExtractor
     ├── EducationExtractor
     └── ExperienceExtractor

This keeps the system replaceable.

For example, if we later introduce an AI-powered extractor, the rest of the system should not need major changes.

---

25. Requirement Extraction Pipeline

Job Description
       ↓
Text Cleaning
       ↓
Section Detection
       ↓
Requirement Extraction
       ↓
Skill Normalization
       ↓
Required / Preferred Classification
       ↓
Experience Extraction
       ↓
Education Extraction
       ↓
Eligibility Extraction
       ↓
Structured Job Requirements

---

26. Skill Normalization

Different employers may use different names for similar technologies.

For example:

Spring Boot
SpringBoot
Spring Boot Framework

should map to an appropriate canonical skill representation.

Likewise:

Postgres
PostgreSQL

may map to:

PostgreSQL

A controlled skill dictionary/taxonomy should be introduced.

---

27. Matching Engine

The matching engine compares:

User Profile
+
Confirmed Skills
+
Experience
+
Education
+
Preferences

        VS

Job Requirements

It should produce:

JobMatch
--------
matchScore
matchedSkills
missingSkills
matchedRequirements
missingRequirements
preferenceMatches
eligibilityStatus
explanation

---

28. Matching Strategy

The initial matching engine should be rule-based and explainable.

Example conceptual evaluation:

Required skills       → evaluate
Preferred skills      → evaluate
Experience            → evaluate
Education             → evaluate
Eligibility           → evaluate
Preferences           → evaluate

The final score should be calculated from clearly documented rules.

The exact weights will be configurable rather than hard-coded throughout the application.

---

29. Hard Requirements vs Soft Requirements

This distinction is critical.

Hard requirement

Example:

Must have Bachelor's degree

If the user does not meet it, the system should flag it clearly.

Soft/preferred requirement

Example:

Docker is a plus

Missing Docker should not be treated the same way as missing a mandatory requirement.

---

30. Match Explanation

The matching engine must produce evidence.

Example:

Match Analysis

Required Skills:
Java             MATCH
Spring Boot      MATCH
SQL              MATCH
Docker           MISSING

Experience:
Entry-level      MATCH

Education:
Bachelor's       MATCH

Preferences:
Backend          MATCH
Remote           MATCH

This explanation should be generated from structured data, not merely from an opaque AI response.

---

31. AI Usage Strategy

AI may be introduced where traditional deterministic rules are insufficient.

Potential AI use cases:

- Resume information extraction
- Job requirement extraction
- Semantic skill matching
- Similar-skill identification
- Natural-language explanation

AI should not be the source of truth for:

- Application URLs
- Job IDs
- Dates
- Employer-provided requirements
- Eligibility facts

Those should come from structured source data whenever possible.

---

32. REST API Design

Initial API groups:

/api/v1/auth
/api/v1/users
/api/v1/profile
/api/v1/resumes
/api/v1/jobs
/api/v1/matches
/api/v1/applications
/api/v1/saved-jobs
/api/v1/notifications

---

33. Authentication APIs

POST /api/v1/auth/register
POST /api/v1/auth/login

Future:

POST /api/v1/auth/refresh
POST /api/v1/auth/logout

---

34. Resume APIs

POST   /api/v1/resumes
GET    /api/v1/resumes
GET    /api/v1/resumes/{id}
DELETE /api/v1/resumes/{id}

---

35. Job APIs

GET /api/v1/jobs
GET /api/v1/jobs/{id}

Filtering:

GET /api/v1/jobs?location=remote
GET /api/v1/jobs?type=INTERNSHIP
GET /api/v1/jobs?skill=JAVA

Pagination:

GET /api/v1/jobs?page=0&size=20

---

36. Matching APIs

GET /api/v1/matches
GET /api/v1/matches/{jobId}
POST /api/v1/matches/recalculate

The system should generally calculate matches asynchronously when large numbers of jobs are processed.

---

37. Application APIs

POST   /api/v1/applications
GET    /api/v1/applications
GET    /api/v1/applications/{id}
PATCH  /api/v1/applications/{id}/status
DELETE /api/v1/applications/{id}

The application entity represents the user's tracking of an external application.

CareerScout does not initially submit the external application itself.

---

38. Saved Job APIs

POST   /api/v1/saved-jobs/{jobId}
GET    /api/v1/saved-jobs
DELETE /api/v1/saved-jobs/{jobId}

---

39. Notification Architecture

Matching Engine
      ↓
Relevant Match
      ↓
Notification Queue/Service
      ↓
Email Template
      ↓
Email Provider
      ↓
User

The email provider should be hidden behind an internal interface.

Conceptually:

EmailService
     │
     └── EmailProvider
            │
            └── Resend implementation

This allows another provider to be substituted later.

---

40. Scheduled Jobs

Spring scheduling will initially be used.

Possible scheduled processes:

Job Discovery

Runs periodically to discover new jobs.

Job Processing

Processes newly ingested jobs.

Matching

Matches new jobs against relevant user profiles.

Email Digest

Generates and sends scheduled recommendations.

Conceptually:

Every N hours
     ↓
Discover
     ↓
Process
     ↓
Match
     ↓
Notify

The exact schedule will be configurable.

---

41. Security Architecture

Security requirements include:

- Password hashing.
- Authentication.
- Authorization.
- Secure API endpoints.
- Input validation.
- File validation.
- Access control.
- Secure secrets management.

Protected resources must verify the authenticated user's ownership.

Example:

A user must not be able to request another user's resume merely by changing:

/resumes/123

to:

/resumes/124

---

42. File Security

Resume uploads must be validated.

Checks should include:

- Allowed file types.
- File size.
- Filename handling.
- Content validation.
- Secure storage.
- Access authorization.

Uploaded files should not automatically be exposed publicly.

---

43. Exception Handling

The backend should use centralized exception handling.

Example categories:

ResourceNotFoundException
ValidationException
UnauthorizedException
ForbiddenException
DuplicateResourceException
ExternalSourceException
FileProcessingException

The API should return consistent error responses.

Example:

{
    "timestamp": "...",
    "status": 400,
    "error": "VALIDATION_ERROR",
    "message": "Invalid resume file",
    "path": "/api/v1/resumes"
}

---

44. Logging

Important events should be logged.

Examples:

- Authentication events
- Resume processing failures
- Source connector failures
- Job ingestion
- Matching failures
- Email failures

Sensitive information such as passwords and tokens must never be logged.

---

45. Testing Strategy

Testing will occur at multiple levels.

Unit Tests

Test:

- Matching rules
- Skill normalization
- Requirement classification
- Validators
- Services

Integration Tests

Test:

- REST APIs
- Database operations
- Authentication
- Job ingestion

End-to-End Testing

Eventually test the complete workflow:

Upload Resume
→ Process
→ Discover Job
→ Extract Requirements
→ Match
→ Generate Notification

---

46. API Documentation

All public APIs should be documented.

Documentation should include:

- Endpoint
- HTTP method
- Authentication
- Request body
- Parameters
- Response
- Error responses

OpenAPI-compatible documentation will be used.

---

47. Database Indexing

Indexes should eventually be added for frequently queried fields.

Potential indexes:

User.email
Job.externalJobId
Job.postedAt
Job.status
Job.companyId
Job.sourceId
Skill.normalizedName
Application.userId
JobMatch.userId
JobMatch.jobId

Indexes will be validated against actual query patterns rather than added blindly.

---

48. Transaction Management

Operations that modify multiple related entities should use appropriate transaction boundaries.

Example:

Create Application
    ↓
Validate Job
    ↓
Check Duplicate Application
    ↓
Save Application

This should behave as one consistent database operation.

---

49. Performance Strategy

MVP:

- PostgreSQL indexing
- Pagination
- Efficient queries
- Batch processing
- Avoid unnecessary database calls

Later:

- Redis caching
- Async processing
- Message queues
- Search engine
- Horizontal scaling

---

50. Scalability Strategy

Initial:

Single Spring Boot application
        +
PostgreSQL

Future:

                    Load Balancer
                         │
             ┌───────────┼───────────┐
             ▼           ▼           ▼
          Backend     Backend     Backend
             │           │           │
             └───────────┼───────────┘
                         │
                    PostgreSQL
                         │
             ┌───────────┴───────────┐
             ▼                       ▼
          Redis                  Message Queue

Microservices will only be introduced if there is a demonstrated need.

---

51. Docker Architecture

The application should eventually be containerized.

Development environment:

Docker Compose
│
├── CareerScout Backend
└── PostgreSQL

Future:

Backend
PostgreSQL
Redis
Message Broker

---

52. CI/CD

GitHub-based CI/CD should eventually perform:

Push Code
    ↓
Build
    ↓
Run Tests
    ↓
Static Checks
    ↓
Build Docker Image
    ↓
Deploy

Deployment strategy will depend on the hosting provider selected later.

---

53. Configuration Management

Environment-specific configuration should not be hard-coded.

Examples:

DATABASE_URL
DATABASE_USERNAME
DATABASE_PASSWORD
JWT_SECRET
EMAIL_API_KEY
STORAGE_CONFIGURATION

Secrets must be stored through environment/secret-management mechanisms rather than committed to Git.

---

54. Observability

Future production version should provide:

- Application logs
- Error monitoring
- Health checks
- Metrics
- Job-source success/failure monitoring
- Email delivery monitoring

Spring Boot Actuator can be introduced for health and metrics.

---

55. Source Reliability

External sources can fail or change format.

Each connector should therefore support:

SUCCESS
PARTIAL_SUCCESS
FAILED

The system should record connector failures without stopping the entire job-discovery pipeline.

One broken source should not prevent other sources from being processed.

---

56. Rate Limiting and Responsible Access

The discovery engine must:

- Respect source limits.
- Avoid excessive requests.
- Prefer official APIs/feeds when available.
- Use reasonable synchronization intervals.
- Respect applicable terms and access restrictions.

The system must not bypass:

- CAPTCHA
- Authentication
- Anti-bot mechanisms
- Access controls

---

57. Data Flow — Complete System

                   ┌───────────────┐
                   │    Resume     │
                   └───────┬───────┘
                           │
                           ▼
                    Resume Parser
                           │
                           ▼
                    User Profile
                           │
                           │
                           ▼
                     MATCH ENGINE
                           ▲
                           │
Job Sources → Connectors → Jobs
                           │
                           ▼
                    Job Processor
                           │
                           ▼
                 Job Requirements
                           │
                           ▼
                     MATCH ENGINE
                           │
              ┌────────────┴────────────┐
              ▼                         ▼
        Match Results             Skill Gaps
              │                         │
              └────────────┬────────────┘
                           ▼
                     Notification
                           │
                           ▼
                         Email

---

58. Development Phases

Phase 1 — Foundation

Learn/build:

- Java fundamentals
- OOP
- Maven
- Spring Boot basics
- REST
- PostgreSQL
- JPA/Hibernate

Build:

- Project skeleton
- Database connection
- Basic user APIs

---

Phase 2 — Authentication

Build:

- Registration
- Login
- Password hashing
- Security
- Authorization

---

Phase 3 — Profile & Resume

Build:

- Profile
- Resume upload
- Resume text extraction
- Structured resume data

---

Phase 4 — Job Engine

Build:

- Job entity
- Source abstraction
- First permitted connector
- Job ingestion
- Normalization
- Deduplication

---

Phase 5 — Requirement Engine

Build:

- Skill extraction
- Requirement classification
- Experience extraction
- Education extraction

---

Phase 6 — Matching Engine

Build:

- Rule-based matching
- Required/preferred distinction
- Match score
- Explainable results
- Skill gaps

---

Phase 7 — Automation

Build:

- Scheduled discovery
- Automatic processing
- Automatic matching
- Email digest

---

Phase 8 — Engineering Quality

Add:

- Unit tests
- Integration tests
- API documentation
- Logging
- Exception handling
- Security hardening

---

Phase 9 — Deployment

Add:

- Docker
- CI/CD
- Production database
- Deployment
- Monitoring

---

59. MVP Technical Definition

The MVP technical system will contain:

Java
Spring Boot
Spring Security
REST APIs
PostgreSQL
JPA/Hibernate
Maven
JUnit/Mockito
Git/GitHub
Docker
Email Provider

with these major modules:

Auth
User
Resume
Job
Discovery
Requirement Processing
Matching
Application Tracking
Notification

---

60. Future Technical Evolution

CareerScout can evolve toward:

                    API Gateway
                         │
       ┌─────────────────┼──────────────────┐
       ▼                 ▼                  ▼
 Resume Service     Job Service      Matching Service
       │                 │                  │
       ▼                 ▼                  ▼
 Resume Storage      Job DB             Match DB
                         │
                         ▼
                    Search Engine
                         │
                         ▼
                   Message Queue
                         │
                         ▼
                 Notification Service

This architecture is intentionally future-ready without forcing premature microservices.

---

61. Key Engineering Principles

CareerScout will follow these principles:

1. Explainability

Recommendations should be explainable.

2. Modularity

Each major responsibility should have a clear boundary.

3. Security First

Resume and account data must be protected.

4. Source Transparency

The original job source/application URL should be preserved.

5. User Control

The system recommends; the user decides whether to apply.

6. Extensibility

Adding another permitted job source should not require rewriting the core system.

7. Testability

Business logic should be testable independently.

8. Production Mindset

The project should be designed as a real application rather than a classroom CRUD demonstration.

---

62. Final Technical Definition

CareerScout will initially be implemented as a modular Spring Boot monolith backed by PostgreSQL.

It will use:

REST APIs
Spring Security
JPA/Hibernate
Scheduled processing
Source connector abstraction
Resume processing pipeline
Explainable matching engine
Email notification service
Automated testing
Docker
CI/CD

The architecture will allow future integration of:

Redis
Message queues
Search engines
AI/LLM services
Additional job sources
Microservices

without requiring a complete rewrite of the core product.

---

63. Final System Contract

The complete MVP must satisfy this technical workflow:

USER
 ↓
Authentication
 ↓
Resume Upload
 ↓
Resume Processing
 ↓
Structured Profile
 ↓
User Preferences
 ↓
Scheduled Job Discovery
 ↓
Source Connector
 ↓
Job Normalization
 ↓
Deduplication
 ↓
Requirement Extraction
 ↓
Matching Engine
 ↓
Explainable Match
 ↓
Application Requirements
 ↓
Relevant Job
 ↓
Email Notification
 ↓
Original Application Source
 ↓
USER DECISION

This TRD is the technical baseline for CareerScout v1.0.
