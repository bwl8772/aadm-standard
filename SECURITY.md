# Security Policy

## Supported versions

This repository publishes **documentation, templates, and examples** only. It does not ship a runtime or dependency surface that receives versioned security patches in the traditional sense.

If this repository later adds generated artifacts or tooling with explicit releases, maintainers will document supported release lines here.

## Reporting a vulnerability

**Please do not open a public issue for security-sensitive reports.**

Instead, contact repository maintainers through a private channel:

1. Use GitHub **Private vulnerability reporting** for this repository (if enabled), or  
2. Email maintainers using the contact listed in [`community/maintainers.md`](community/maintainers.md).

Include:

- A clear description of the issue and its impact  
- Steps to reproduce (if applicable)  
- Any suggested fix or mitigation (optional)

We aim to acknowledge reports within a few business days. Disclosure timelines will balance fix readiness with reporter coordination.

## Scope

Reports appropriate for this project include:

- Content that could mislead readers into unsafe patterns (for example, guidance that weakens authorization boundaries)  
- Malicious or unintended executable content embedded in this repo  
- Supply-chain concerns tied to **this repository’s** contents or automation  

Reports about **third-party products**, **specific deployments**, or **unrelated applications** should go to the respective vendor or project.

## Safe defaults for adopters

When you implement AADM-aligned systems:

- Treat **authorization** and **data access** as application responsibilities—documentation does not replace enforcement in code.  
- Prefer **least privilege**, **explicit boundaries**, and **reviewable automation** over implicit trust in agents or tools.  
- Secure any **agent or assistant runtimes** your organization operates like internal APIs (TLS, scoped credentials, monitoring)—**this documentation repo does not host those systems**.
