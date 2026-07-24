---
layout: post
title: "Infrastructure as Code for Careers: Building an Automated Resume CI/CD Pipeline"
date: 2026-07-24
categories: [DevOps, CI-CD, Automation, SecOps]
tags: [github-actions, gradle, markdown, automation, pandoc, security]
---

# Case Study: Single Source of Truth (SSoT) Resume CI/CD Pipeline

## 💡 Motivation: Productive Toil Elimination (I Hate MS Word)
The driving force behind this project was a desire to completely eliminate manual documentation upkeep, combined with a deep frustration with traditional word processors. 
* **The Microsoft Word Problem:** Traditional WYSIWYG editors are fundamentally broken for code-centric documentation. A single unintended keystroke can destroy layout grids, page margins, and font inheritance across an entire document. 
* **Leveraging "Engineered Laziness":** The best automation stems from a refusal to repeat boring, manual tasks. Rather than wasting time fighting formatting boxes or manually exporting file variants every time a resume bullet is updated, it was vastly more efficient to spend a few hours engineering a permanent, programmatic solution. 


## 📌 Executive Summary
Engineered an automated, multi-artifact compilation pipeline that treats career documentation with production-grade DevOps discipline. By establishing a single `resume.md` file as a Single Source of Truth (SSoT), the pipeline automates formatting, build validation, and compilation of web, PDF, DOCX, and plain-text assets. To align with modern SecOps compliance frameworks, the pipeline features an automated programmatic data-scrubbing phase to strip Personally Identifiable Information (PII) mid-flight before shipping build artifacts to a public-facing hosting environment.

## 📉 The Operational Problem
* **Fragile Multi-Formatting:** Manually maintaining separate Word documents, PDFs, and plain-text resumes leads to configuration drift, typos, and formatting mismatch across platforms.
* **PII Security Exposure:** Publicly hosting a resume on GitHub or a portfolio site risks exposing highly sensitive personal telemetry (phone numbers, private emails, exact location) to malicious scrapers and bad actors.
* **Manual Toil:** Manually recompiling, checking formatting, and uploading assets to web servers introduces operational friction and human error.

## 🏗️ Pipeline Architecture & Data Flow

```text
 [Private Repository] 
         │
         ▼ (git push resume.md)
 [GitHub Actions Runner]
         │
         ├─► Step 1: Execute Regex Engine (Strip PII / Target Fields)
         ├─► Step 2: Invoke Gradle Build Engine
         │             ├─► Compile via Pandoc/Weasyprint ──► [PDF / DOCX]
         │             └─► Sanitize raw markdown ──────────► [Clean TXT]
         ▼
 [Cross-Repo Secure Dispatch] ──► [Public Pages Repo] ──► [Live Portfolio Site]
```

### 1. Build Orchestration & Dependency Engine
* **The Engine:** Selected **Gradle** as the task-execution runner to manage compilation logic outside of standard software artifact paths. 
* **The Compilation Layer:** Integrated markdown parsers and engines (such as Pandoc or WeasyPrint) natively into custom Gradle build scripts to ensure pixel-perfect rendering across PDF and DOCX binaries.

### 2. Automated Mid-Pipeline SecOps Scrubbing
* **The Layer:** Implemented a robust parsing phase utilizing customized regex patterns within the GitHub Actions runner environment.
* **The Execution:** Before any public exposure occurs, the runner scans the raw SSoT file, extracts high-risk strings (phone numbers, physical addresses, private email schemas), and replaces them with environment-safe variables.
* **The Dispatch:** The sanitized artifacts are programmatically bundled and pushed via a secure cross-repository deployment token directly into a public repository mapped to GitHub Pages.

## 🚀 Engineering Impact & Results
* **Eliminated Document Drift:** Guaranteed 100% text, layout, and content parity across all job board application formats through the SSoT framework.
* **Zero-Touch Maintenance:** Compressed the entire modification and deployment cycle down to a standard `git push`, reducing publishing overhead to seconds.
* **Architectural Blueprint:** Serves as a live, auditable proof-of-concept for recruiters, instantly proving hands-on mastery of CI/CD, pipeline logic, and security compliance.
