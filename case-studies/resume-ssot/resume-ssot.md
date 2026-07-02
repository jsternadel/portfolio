---
layout: post
title: "Case Study: Single-Source Document Automation Engine"
subtitle: "Bypassing SaaS bloat with Kotlin DSL, WeasyPrint, and a custom Debian WSL toolchain"
date: 2026-05-24
---

## Context & Objective
Maintaining professional profiles across PDF, web (GitHub Pages), Plain Text (ATS optimization), and Word (DOCX) formats traditionally introduces **formatting drift** and manual operational overhead. The goal was to build a local, deterministic compilation pipeline that translates a single `resume.md` file into four identical, production-ready target assets simultaneously using automated CI/CD.

## The Architectural Breakdown (The "Why")
Modern developer experience (DX) is heavily reliant on bloated cloud dependencies, brittle npm package trees, and rigid web-framework wrappers. Standard browser "Print-to-PDF" utilities fail at typographic layout, leaving orphan headings stranded at the bottom of pages due to continuous viewport rendering. 

Furthermore, cloud-hosted static site generators (like Jekyll) impose opinionated, hidden security gates that clobber modular CSS architectures (`@import` rules) unless forced into high-specificity overrides (`html body`).

## The System Design & Engineering Pass

### 1. Isolated Subsystem Assembly
To ensure local compilation matches cloud runner execution identically, a custom, ultra-lightweight **Debian 12 (bookworm)** environment was compiled from upstream binary sources using `debootstrap` and isolated directly on a secondary physical hard drive (`D:\wsl\installs`). This bypassed Microsoft Store dependencies and established a reproducible, containerized local lab environment.

### 2. Symmetrical Build Orchestration
A custom **Kotlin DSL Gradle task** maps compilation phases deterministically. The asset pipeline uses a linear, high-utility task execution graph:
* **PII Sanitization:** A custom string processing engine uses optimized regular expressions to automatically mask tracking strings and private contact metrics, protecting data before pushing it to public repositories.
* **Vector Layout Computation:** Unlike screens, print layouts require absolute coordinate calculations. The engine binds **Pandoc** and **WeasyPrint** directly, mapping typography elements cleanly to native OS vector font sets (`fonts-noto-core`) to satisfy strict W3C Paged Media layout constraints without brittle web wrappers.
* **Conditional Profiling:** To keep production artifacts pure, intermediate debug HTML compilation passes are isolated behind a dynamic project property flag (`-Pdebug`), dynamically modifying Gradle's Directed Acyclic Graph (DAG) maps at configuration runtime.

### 3. Defensive DevOps & Cleanup
The GitHub Actions workflow includes a rigorous workspace reset loop. To prevent file accumulation decay (where renamed or deleted layout files pollute the public deployment tree), a defensive shell execution pass (`find . -maxdepth 1 ! -name '.git' -exec rm -rf {} +`) completely wipes the remote target directory before laying down the fresh asset structure.

## Observable Engineering Outcomes
* **Zero Layout Drift:** Structural text updates made to `resume.md` automatically cascade to all four distribution formats within **~30 seconds** of a git push.
* **Parity:** Local WSL environment output matches the GitHub Actions worker execution identically, completely eliminating cloud configuration guesswork.
* **Optimized Footprint:** The entire compiled base OS infrastructure takes up only **~88MB** of host storage, achieving lightning-fast initialization times compared to standard heavy VM distributions.