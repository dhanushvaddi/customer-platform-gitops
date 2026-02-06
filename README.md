Customer Platform – Multi-Environment GitOps, Progressive Delivery & SRE Observability
Overview

This repository demonstrates a production-grade DevOps and SRE platform implemented on Kubernetes using GitOps principles.
It simulates how modern engineering teams deploy, monitor, and operate customer-facing microservices with high reliability and controlled risk.

The project is designed to reflect current industry practices, not a simple tutorial setup.

Key Goals

Implement GitOps-driven continuous delivery

Enforce environment isolation (Dev → Prod)

Deploy applications using canary strategy

Enable full observability using metrics and dashboards

Define and validate SLIs and SLOs

Perform chaos engineering to test resilience

Practice real production troubleshooting workflows

Architecture Summary

High-level workflow:

Developer Commit
   ↓
Git Repository (GitOps)
   ↓
Argo CD (Projects & RBAC)
   ↓
Dev Environment (practice namespace)
   ↓
Canary Deployment & Analysis
   ↓
Production Environment (prod namespace)
   ↓
Observability (Prometheus, Grafana)
   ↓
Chaos Engineering Validation

Technology Stack
Category	Tool
Container Orchestration	Kubernetes (Minikube – multi-node)
GitOps	Argo CD
Progressive Delivery	Argo Rollouts
Monitoring	Prometheus
Visualization	Grafana
Alerting	Alertmanager
Chaos Engineering	Chaos Mesh
Packaging	Helm
Ingress	NGINX Ingress Controller
Namespace Design

Namespaces are separated by responsibility, following enterprise standards:

Namespace	Purpose
argocd	GitOps control plane
practice	Development / non-production
prod	Production workloads
monitoring	Metrics, dashboards, alerting
chaos	Chaos engineering experiments
ingress-nginx	External traffic entry

This separation limits blast radius and enforces ownership boundaries.

Repository Structure
customer-platform-gitops/
│
├── argocd/
│   ├── project-dev.yaml
│   ├── project-prod.yaml
│   ├── app-dev.yaml
│   └── app-prod.yaml
│
├── envs/
│   ├── dev/
│   │   └── customer-api/
│   │       ├── rollout.yaml
│   │       ├── service.yaml
│   │       ├── configmap.yaml
│   │       └── secret.yaml
│   │
│   └── prod/
│       └── customer-api/
│           ├── rollout.yaml
│           ├── service.yaml
│           ├── ingress.yaml
│           ├── hpa.yaml
│           ├── servicemonitor.yaml
│           └── analysis-template.yaml
│
├── helm/
│   └── customer-api/
│
├── chaos/
│   └── pod-kill.yaml
│
└── README.md

GitOps and RBAC with Argo CD

Argo CD Projects are used to enforce environment boundaries:

Dev Project

Can deploy only to the practice namespace

Prod Project

Can deploy only to the prod namespace

This prevents accidental production changes and supports compliance and audit requirements.

Multi-Environment Strategy
Development Environment (practice)

Faster feedback cycle

Lower replica count

No external ingress

Canary testing without production risk

Production Environment (prod)

Canary deployments with traffic shifting

Horizontal Pod Autoscaling

Ingress-based traffic exposure

Automated rollback based on metrics

Progressive Delivery (Canary)

Applications are deployed using a canary strategy:

20% traffic → observe metrics

50% traffic → observe metrics

100% traffic → promote to stable

Promotion or rollback decisions are automated using live metrics.

Observability and Monitoring
Metrics Flow
Application (/metrics endpoint)
   ↓
Prometheus (ServiceMonitor)
   ↓
Grafana Dashboards

Core PromQL Queries

Request rate:

sum(rate(http_requests_total[1m]))


Error rate:

sum(rate(app_errors_total[1m]))


Latency (p95):

histogram_quantile(
  0.95,
  sum(rate(http_request_duration_seconds_bucket[5m])) by (le)
)

SLI and SLO Definition
Example SLIs

Request success rate

Request latency

Service availability

Example SLOs

Availability: 99.9%

Latency: p95 < 300ms

Error Budget

0.1% allowable downtime per month

Error budgets are used to balance reliability with deployment velocity.

Chaos Engineering

Chaos experiments are executed in the chaos namespace to validate system resilience.

Example experiment:

Randomly terminate production pods

Observe service continuity

Verify auto-healing and SLO impact

This confirms the system can tolerate real-world failures.

Validation and Operations

Operational checks performed:

Argo CD sync and health status

Rollout health and progression

Prometheus target availability

Grafana dashboard accuracy

Automated rollback on failure

Recovery after chaos events

These steps mirror real on-call SRE workflows.

DevOps and SRE Responsibilities (Applied)
DevOps Focus	SRE Focus
CI/CD automation	SLIs and SLOs
Deployment speed	Reliability engineering
Pipeline optimization	Incident response
Build and release	Error budget management

This project demonstrates both roles working together.

Key Learnings

GitOps simplifies consistency and recovery

Canary deployments significantly reduce release risk

Observability is essential for safe automation

Chaos engineering increases confidence in production

SRE practices improve long-term system stability

Conclusion

This project implements a realistic DevOps and SRE platform using Kubernetes and GitOps.
It provides hands-on experience with deployment automation, monitoring, reliability engineering, and failure handling, making it directly applicable to real production environments and technical interviews.

Future Enhancements

Alertmanager integration with Slack or email

Blue-Green deployments with manual approval gates

Vault-based secret management

Multi-cluster GitOps

Service mesh observability

Author Note

This repository was built as a hands-on industry simulation to practice modern DevOps and SRE workflows using Kubernetes and GitOps.
