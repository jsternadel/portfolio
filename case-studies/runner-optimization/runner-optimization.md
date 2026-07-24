---
layout: post
title: "Defending the Pipeline: Building an Offline-First Custom Gitea/act_runner Image"
date: 2026-07-24
categories: [DevOps, Containerization, CI-CD, Infrastructure]
tags: [docker, gitea, act-runner, cicd, optimization, self-hosted]
---

# Case Study: Self-Hosted CI/CD Optimization & Upstream Outage Hardening

## 📌 Executive Summary
Designed and built a highly optimized, custom Docker runner image for a local **Gitea + act_runner** CI/CD infrastructure. This project was initiated to completely eliminate a high-latency **8-minute deployment bottleneck** and insulate the automation pipeline from external upstream CDN/repository dependencies. By pre-baking the complete compilation toolchain (Java, Gradle, Pandoc, WeasyPrint) into a single baseline image layer, local pipeline execution times dropped by **over 90%**, ensuring 100% build availability even during total public network degradation.

## 💡 Motivation: Defending against Upstream Cascading Failures
Relying on public hosting environments or downloading heavy software packages dynamically introduces significant infrastructure vulnerabilities:
* **The Canonical CDN Bottleneck:** When public package mirrors or CDNs experience service interruptions, local automation pipelines break instantly, halting deployments.
* **The 8-Minute Build Debt:** Forcing an ephemeral container runner to pull, download, and install massive external packages (such as layout engines or system fonts) on *every single push* introduces unacceptable operational toil. I chose to build a permanent, cached local solution.

## 🏗️ Optimized Runner Architecture

```text
 [Generic Ubuntu Base Image] ---> Installs Java, Pandoc, Fonts, WeasyPrint (8 mins)
                                          │
                                          ▼ [Layer Flattening & Optimization]
                            [Pre-Baked Custom Runner Image]
                                          │
    ┌─────────────────────────────────────┴─────────────────────────────────────┐
    ▼ (git push to Gitea)                                                       ▼ (Upstream Outage)
[Local act_runner Daemon]                                                  [Canonical CDN Offline]
    │                                                                           │
    ├─► Instantly Mounts Pre-Baked Container                                    ❌ (Zero Impact)
    ├─► Zero Download Latency (Local Cache)                                     │
    ▼                                                                           ▼
[Live Multi-Format Compilation Completes in Seconds]                        [Pipeline Stays 100% Online]
```

### 1. Multi-Stage Toolchain Pre-Baking
Instead of pulling software binaries dynamically during execution, the build utilities are shifted entirely left into the immutable image-building process:
* **Runtime Layer:** Bundles a minimal headless Java Runtime Environment (JRE) required to orchestrate Gradle tasks natively.
* **Compilation Layer:** Embeds pinned versions of `pandoc` along with required system layout fonts and styling engines to handle immediate Markdown-to-PDF conversion without requiring runtime network fetches.

### 2. act_runner Environment Integration
* Configured the local Gitea instance to route specific compilation tags (e.g., `runs-on: custom-gradle-runner`) straight to the self-hosted Docker daemon.
* Eliminates container startup overhead by relying on local image layers already resident on the host storage network.

## 🚀 Business Impact & Results
* **Velocity Maximization:** Eradicated the 8-minute download-and-install phase, bringing overall local pipeline execution times down to **under 20 seconds**.
* **Absolute Network Isolation:** Achieved 100% architectural self-sufficiency. The pipeline functions perfectly during public internet disconnects or regional mirror outages.
* **Resource Conservation:** Substantially minimized local area network traffic and storage thrashing across the lab virtualization footprint.
