# CLAUDE.md - FHIR-API

## Project Overview

FHIR-API provides FHIR (Fast Healthcare Interoperability Resources) R4 standard compliance and NDHM (National Digital Health Mission) / ABDM (Ayushman Bharat Digital Mission) integration for the AMRIT platform. It handles ABHA (Ayushman Bharat Health Account) card creation/login, health ID validation, care context management, FHIR resource generation, and e-Aushadhi drug inventory integration.

## Tech Stack

- Java 17, Spring Boot 3.2.2, Maven
- Spring Data JPA / Hibernate, MySQL 8.0
- MongoDB (for NDHM responses, care contexts, AMRIT resources)
- Redis for session management
- Quartz scheduler for background jobs
- HAPI FHIR library for FHIR R4 resource handling
- Lombok, MapStruct
- SpringDoc OpenAPI (Swagger UI at `/swagger-ui.html`)
- ECS logging (logback-ecs-encoder)
- JaCoCo for test coverage
- Packaged as WAR for Wildfly deployment

## Build & Run

```bash
mvn clean install -DENV_VAR=local          # Build
mvn spring-boot:run -DENV_VAR=local        # Run locally
mvn -B package --file pom.xml -P <profile> # Package WAR (dev, local, test, ci, uat)
mvn test                                    # Run tests
```

Environment config: `src/main/resources/common_<ENV_VAR>.properties` is copied to `application.properties` at build time.

## Key Packages (`com.wipro.fhir`)

- **controller/** - REST endpoints:
  - `healthID/` - ABHA/Health ID creation and management
  - `healthIDvalidate/` - Health ID validation
  - `healthCard/` - Health ID card generation
  - `carecontext/` - Care context linking and management
  - `generateresource/` - FHIR resource generation
  - `patientdatahandler/` - Patient data gateway and higher health facility integration
  - `eaushdhi/` - e-Aushadhi drug inventory integration
  - `facility/` - Facility management
  - `v3/abha/` - ABDM v3 APIs for ABHA creation and login
- **service/** - Business logic (mirrors controller structure):
  - `ndhm/` - NDHM callback/gateway services
  - `resource_gateway/` - FHIR resource gateway
  - `resource_model/` - FHIR resource model builders
  - `atoms/feed/bahmni/` - Bahmni EMR Atom feed integration
  - `api_channel/` - API channel routing
- **data/** - JPA entities and data models:
  - `resource_model/` - FHIR resource data models (DiagnosticReport, Condition, AllergyIntolerance, MedicationRequest, etc.)
  - `mongo/` - MongoDB document models (care_context, amrit_resource)
  - `v3/abhaCard/` - ABDM v3 data models
  - `atoms/feed/bahmni/` - Bahmni feed models
  - `healthID/`, `healthID_validate/` - Health ID data models
  - `e_aushdhi/` - e-Aushadhi data models
- **repo/** - JPA and MongoDB repositories
- **utils/** - Utilities (Redis, HTTP, validation, session, gateway, exception handling)
- **config/** - Application and Quartz scheduler configuration

## Architecture Notes

- Dual database: MySQL for relational data, MongoDB for NDHM responses and FHIR resources
- Integrates with ABDM (formerly NDHM) APIs for ABHA card lifecycle (create, validate, login)
- FHIR R4 resources generated from AMRIT clinical data (encounters, observations, conditions, medications, etc.)
- Care context management links patient visits to ABDM health records
- Quartz scheduler handles async NDHM callbacks and background processing
- Bahmni Atom feed integration for external EMR interoperability
- e-Aushadhi integration for government drug inventory system
- Artifact ID: `FHIR_API`, group: `com.wipro.fhir.r4`
