# Compliance checklist

Use this checklist when adopting AADM practices alongside organizational compliance obligations (privacy, sector regulations, internal risk policies). It is **guidance**, not legal advice. Align entries with your counsel and risk owners.

## Governance

- [ ] **Standard ownership** — Named architects or leads accountable for layer mapping decisions  
- [ ] **Change records** — Material architecture shifts trace to reviewed intent artifacts  
- [ ] **Third-party review** — Vendor or model changes assessed for data handling  

## Data protection

- [ ] **Classification** — Data classes attached to scenarios and interfaces  
- [ ] **Minimization** — Agents and tools receive only necessary context  
- [ ] **Residency** — Processing and storage locations documented per workload  
- [ ] **Retention** — Lifecycle matches policy; deletion paths tested  

## Security

- [ ] **Threat modeling** — Updates when trust boundaries move (new integrations, agents, MCP servers)  
- [ ] **Secrets** — No secrets in repos; rotation tested  
- [ ] **Authorization** — Domain checks present where sensitive actions occur ([AUTH-aware delivery](auth-aware-delivery.md))  
- [ ] **Dependency posture** — Supply-chain practices defined for AI/tooling stacks  

## Reliability and safety

- [ ] **Human gates** — Irreversible operations require explicit approval ([Human in the loop](../standards/human-in-the-loop.md))  
- [ ] **Rollback** — Safe reversal documented for releases touching critical paths  
- [ ] **Observability** — Signals exist to detect abuse, drift, or systemic failure  

## Verification

- [ ] **Tests mapped to layers** — Evidence ties checks to the layers they protect  
- [ ] **Periodic audits** — Sampling that templates and docs match production reality  

## Documentation

- [ ] **Public vs internal** — Clear separation (this repo stays adoption-safe)  
- [ ] **Evidence pointers** — Policies reference concrete controls, not aspirations  

## Sign-off

Record reviewers and dates in your program’s system of record—not in this public repository if sensitive.
