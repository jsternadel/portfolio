---
layout: default
title: 'Joshua Sternadel | {$js-portfolio}'
---

# Joshua Sternadel
## System Architecture & Business Alignment

A non-traditional engineer who leverages a background in artistic creativity to approach technical architecture without the bias of "industry-standard" stagnation. Applies spatial thinking, pattern recognition, and deep-infrastructure logic to solve complex structural problems that traditional engineering teams struggle to resolve. A self-directed technical anchor focused on building clean, frictionless distributed systems that prioritize radical automation, continuous compliance, and mission-critical stability.

### Navigation Hub
*   [**Read Professional Resume**](./resume){:target="_blank"}
*   [**Download PDF**](./joshua-sternadel-resume.pdf){:target="_blank"}
*   [**Download Plain Text**](./joshua-sternadel-resume.txt){:target="_blank"}
*   [**Download DOCX**](./joshua-sternadel-resume.docx){:target="_blank"}

---

### Deep-Dive Architectural Briefs
# Engineering Portfolio & Technical Documentation

Welcome to my systems engineering and automation portfolio. This space hosts production-grade case studies, architectural deep dives, and live automation proofs of concept. My engineering philosophy focuses on extreme ownership, data-driven security compliance, and building resilient infrastructure within highly volatile environments.

---

## Deep-Dive Architectural Briefs
Below are first-principle engineering breakdowns of production solutions and infrastructure frameworks.

* [**Solo-Driven Vulnerability Remediation**](./case-studies/ansible-iac/ansible-iac.md)
    * **Mission:** Leverage Ansible to institute automated, idempotent OS patching within a chaotic laboratory environment.
    * **Context:** Executed as a 100% solo initiative with zero baseline budget, zero corporate training, and developers holding full root access.
    * **Impact:** Flatlined persistent security debt from **100+ monthly vulnerabilities to absolute zero**, causing project managers to assume the Maya monitoring tool had broken.

* [**Automated Security Fleet Bootstrapping**](./case-studies/spirent-onboarding/spirent-onboard.md)
    * **Mission:** Design a modular orchestration framework to safely provision a 5-agent security compliance stack across 132 Spirent Landslide VMs.
    * **Context:** Required programmatic modification of sensitive **Netplan configurations** to inject custom IP routing tables for Illumio VEN without dropping network interfaces.
    * **Impact:** Maintained a **0% network dropout rate** across the entire remote footprint, compressing a 40+ hour high-risk manual workflow into a single-pass automated run.

* [**Self-Hosted CI/CD Optimization**](./case-studies/runner-optimization/runner-optimization.md)
    * **Mission:** Build a highly optimized, pre-baked Docker runner image for a local Gitea + `act_runner` setup.
    * **Context:** Hardened local build pipelines against upstream public mirror and CDN failures (e.g., Canonical repository outages).
    * **Impact:** Reduced environment bootstrap times from **8 minutes to under 20 seconds** via complete local image layer flattening and toolchain caching.

* [**Automated Resume CI/CD Pipeline**](./case-studies/resume-ssot/resume-ssot.md)
    * **Concept:** Treating personal infrastructure as code by enforcing a Single Source of Truth (SSoT).
    * **Motivation:** Elimination of manual document toil and a deep-seated hatred for Microsoft Word. 
    * **Pipeline:** A `git push` to a private repository triggers a GitHub Actions workflow. Gradle compiles a single `resume.md` file into web-optimized PDF, DOCX, and TXT artifacts.
    * **SecOps:** Automated regex steps strip Personally Identifiable Information (PII) mid-pipeline before programmatically dispatching the public-facing assets directly to this GitHub Pages site.
---

<!--
### Deep-Dive Architectural Briefs
Below are first-principles deep dives into enterprise systems I have authored and scaled.

*   [**Project Porcupine**](./case-studies/porcupine-bus)
    *   *The Mission:* Co-architecting a custom, highly reliable asynchronous message bus enabling real-time communication between disparate Fortune 500 enterprise applications.
*   [**Project Locksmith**](./case-studies/locksmit-security)
    *   *The Mission:* Engineering a security-centric middleware layer utilizing HashiCorp Vault with automated, zero-touch handshake protocols to secure local-to-production secrets lifecycle.
*   [**GPS Spherical Geometry Modeling**](./case-studies/gps-geometry)
    *   *The Mission:* Resolving telemetry "teleportation" errors in intermodal logistics by parsing raw NMEA strings and implementing rate-of-acceleration geometry for 99% coordinate accuracy.
-->