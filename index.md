---
layout: default
title: 'Joshua Sternadel | {$js-portfolio}'
---

# Joshua Sternadel

---

### Engineering Portfolio & Technical Documentation
Welcome to my systems engineering and automation portfolio. This space hosts production-grade case studies, architectural deep dives, and live automation proofs of concept. My engineering philosophy focuses on extreme ownership, data-driven security compliance, and building resilient infrastructure within highly volatile environments.

---

> #### [**Taming the Wild West: Flatlining 100+ Monthly Vulnerabilities with Zero Budget**](/portfolio/case-studies/ansible-iac/ansible-iac.html)
> *Leveraging Ansible to institute automated, idempotent OS patching within a chaotic laboratory environment.*

> #### [**Automated Fleet Mobilization: Bootstrapping 132 Spirent VMs and Engineering Resilient Netplan Routing**](./case-studies/spirent-onboard/spirent-onboard.md)
> *Designing a modular orchestration framework to safely provision a 5-agent security compliance stack across 132 Spirent Landslide VMs.*

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

### Resumes
*   [**Read Professional Resume**](./resume){:target="_blank"}
*   [**Download PDF**](./joshua-sternadel-resume.pdf){:target="_blank"}
*   [**Download Plain Text**](./joshua-sternadel-resume.txt){:target="_blank"}
*   [**Download DOCX**](./joshua-sternadel-resume.docx){:target="_blank"}

---