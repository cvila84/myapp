# myapp

App template following some standards

### Developer Portal
- Backstage
  - Maturity
    - Build (CI/CD pipelines as a code, artifacts management, immutable environments, environment promotion gates)
    - Documentation (user guide)
    - Quality (API standards & guidelines, code standards {ex: checkstyle} & guidelines {ex: hexagonal architecture}, SCA grades, test coverage thresholds, mutation testing, contract testing)
    - Deployment (docker, helm, gitops, migration)
    - Security (authn/authz, data ownership, secret management, OWASP SCP)
    - Observability & Alerting (logging, monitoring, tracing, analytics, dashboards, functional ping)
    - Testability (A/B or canary, blue/green deployment readiness, configuration override per request)
    - Configurability (global & role profiles, multi-tenancy, feature toggle)
    - Performance (load & ageing testing, SLO verification)
    - Reliability (chaos testing)
    - Scalability (optimized resource requests, horizontal/vertical scaling testing)
    - Operability (installation, deployment & administration {ex: backup/restore, disaster recovery runbooks} guides, ops scripts {ex: API & DB helpers})
    - Usability (i18n, mobility, accessibility)
  - Build
    - CI/CD (build, test, SCA and SAST reports)
    - Dependencies
    - Docs (Hugo links)
  - Run
    - Observability
    - Alerting
    - Deployments

### Build
- Maven
- Gitea/Gitlab actions
- Devcontainer
- Devfile

### Documentation
- Hugo
  - User guide
  - Installation guide
  - Deployment guide
  - Administration guide
  - Tech documentation (architecture diagrams, DB models)

### Quality
- OpenAPI swagger
- Apicurio (lint)
- Smartbear
- PACT
- squashtest.com
- Selenium/Cypress
- Cobertura
- Sonar
- AllureReport

### Deployment
- Docker
- Helm
- ArgoCD
- Liquibase / Flyway

### Security
- Dex (authn/authz)

### Observability & Alerting
- Otel
- Loki (frontend/backend logging)
- Prometheus
- Thanos (metrics consolidation)
- Zabbix
- PostHog

### Frontend
- React
- Shadcn UI
- TailwindCSS

### Backend
- Quarkus
- Springboot

### Database
- PostgreSQL / MongoDB

### Middleware
- Redis (for async API endpoint)
- Kafka
- Websocket (frontend <-> backend)
