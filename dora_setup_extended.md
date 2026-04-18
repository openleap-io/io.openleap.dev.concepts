# DORA-kompatibles Setup für Softwarefirmen (Audit-Ready, erweitert)

## 1. Zielbild

Dieses Dokument beschreibt ein auditfähiges Setup für Softwarefirmen, die für regulierte Finanzunternehmen entwickeln.
Ziel: Nachweisbare Sicherheit, Resilienz, Governance und kontrollierte Softwareentwicklung.

---

## 2. Referenzarchitektur (High-Level)

### Schichtenmodell

1. Governance & ISMS
2. DevSecOps Pipeline
3. Runtime / Infrastruktur (Cloud / On-Prem)
4. Observability & Incident Response
5. Third-Party & Supply Chain

---

## 3. Konkrete Toolchain (Beispiel)

### Code & Collaboration
- GitHub / GitLab
- Pull Requests mit verpflichtenden Reviews

### CI/CD
- GitHub Actions / GitLab CI / Jenkins

### Security
- SAST: SonarQube, Semgrep
- SCA: Snyk, Dependabot, OWASP Dependency Check
- Container Scan: Trivy
- IaC Scan: Checkov, tfsec

### Secrets Management
- HashiCorp Vault
- AWS Secrets Manager / Azure Key Vault

### Monitoring & Logging
- Prometheus + Grafana (Metrics)
- ELK Stack / OpenSearch (Logs)
- Datadog / Splunk (optional)

### Incident Management
- PagerDuty / Opsgenie
- Jira Service Management

### Policy as Code
- Open Policy Agent (OPA)
- Conftest (für CI/CD Integration)

---

## 4. DevSecOps Pipeline (Audit-Ready)

### Pipeline Schritte

1. Code Commit
2. Pull Request + Reviews (mind. 2)
3. Build
4. Tests (Unit + Integration)
5. Security Scans:
   - SAST
   - SCA
   - Secrets Detection
6. SBOM Generierung
7. Policy Checks (OPA)
8. Deployment (automatisiert)

### Audit-Nachweise
- Build Logs
- Scan Reports
- Review Historie
- Versionierte Artefakte

---

## 5. Policy as Code (Beispiele)

### Beispiel: CVE Block
- Blockiere Deployment bei CVSS > 7

### Beispiel: Review Pflicht
- Kein Merge ohne 2 Approvals

### Beispiel: IaC Security
- Keine offenen Ports (0.0.0.0/0)

### Beispiel: Secrets
- Keine Keys im Code

---

## 6. Prozesse (Audit-Sicht)

### ISMS
- Dokumentierte Risikoanalysen
- Maßnahmen-Tracking

### SSDLC
- Secure Coding Guidelines
- Pflicht Reviews
- Security Gates

### Change Management
- PR-basierte Änderungen
- Freigabeprozesse

### Incident Management
- Klassifizierung (Low/Medium/High)
- Reaktionszeiten definiert
- Post-Mortems

### Monitoring
- Alerts mit Schwellwerten
- zentrale Logs

### Testing & Resilience
- automatisierte Tests
- Failover Tests
- Chaos Engineering

### Third Party
- Vendor Bewertung
- SBOM Pflicht

### Access Management
- RBAC
- MFA
- regelmäßige Reviews

### Backup & Recovery
- tägliche Backups
- regelmäßige Restore Tests

### Dokumentation
- Architekturdiagramme
- Datenflüsse
- Prozessdokumentation

---

## 7. Automatisierungskonzept

### Hoch automatisiert
- CI/CD Pipeline
- Security Scans
- Monitoring & Alerting
- Backup Jobs

### Teilautomatisiert
- Incident Handling
- Third Party Risk

### Manuell
- Risikoentscheidungen
- Governance

---

## 8. Audit-Checkliste (Was Prüfer sehen wollen)

- Gibt es ein ISMS?
- Sind Prozesse dokumentiert?
- Gibt es Nachweise für:
  - Code Reviews?
  - Security Scans?
  - Releases?
- Gibt es Incident Logs?
- Gibt es SBOMs?
- Gibt es Backup-Nachweise?
- Gibt es Monitoring?

---

## 9. Typische Artefakte

- Architekturdiagramm
- Risikoanalyse-Dokument
- Incident Reports
- SBOM Dateien
- CI/CD Logs
- Security Scan Reports

---

## 10. Fazit

Ein auditfähiges Setup bedeutet:
- Prozesse + Tools + Nachweise
- Automatisierung wo möglich
- Verantwortung bleibt beim Menschen

