---
layout: post
title: "Automated Fleet Mobilization: Bootstrapping 132 Spirent VMs and Engineering Resilient Netplan Routing"
date: 2026-07-24
categories: [DevOps, Automation, Networking, Security]
tags: [ansible, netplan, illumio, security, networking, linux]
---

# Case Study: Automated Security Fleet Bootstrapping & Resilient Netplan Routing

## 📌 Executive Summary
Engineered and executed an automated orchestration framework to onboard and standardize a distributed fleet of **132 Spirent Landslide traffic-generation virtual machines**. Designed a modular Ansible architecture to deploy a comprehensive 5-agent security compliance and telemetry stack. A critical engineering milestone of this project involved programmatically modifying **Netplan network configurations** to inject custom IP routing tables required by Illumio VEN. This was accomplished via automation with zero network layer failures or remote host lockouts.

## 📉 The Challenge & High-Stakes Constraints
* **The Netplan Risk:** Netplan is highly sensitive to syntax and configuration errors. A minor mistake during automated editing will instantly drop network interfaces, permanently bricking remote SSH access to the host.
* **Complex Routing Requirements:** Illumio VEN (Virtual Enforcement Node) required explicit, custom policy-based IP routing tables to accurately isolate traffic lanes without interrupting Spirent's heavy simulation payloads.
* **Massive Stack Footprint:** The enterprise mandated the simultaneous bootstrapping of 5 distinct agents onto highly specialized, resource-intensive telecom nodes with zero baseline downtime.

## 📊 Target Security & Telemetry Stack

| Layer | Component | Function / Challenge |
| :--- | :--- | :--- |
| **Micro-Segmentation** | **Illumio VEN** | Requires specialized **Netplan IP routing tables** to segment traffic. |
| **Endpoint Protection** | **SentinelOne** | Next-generation EDR behavior monitoring and threat blocking. |
| **Log Aggregation** | **Splunk Universal Forwarder** | Centralized audit trailing and system telemetry ingestion. |
| **Vulnerability Scanning** | **Tenable Nessus Agent** | Continuous compliance assessment and system scanning. |
| **Legacy Infrastructure** | **Nagios Core Agent** | Classic Web 2.0 interface up-time and state validation. |

## 🏗️ Architecture & Automation Mechanics

```text
       [Ansible Orchestration Control Node]
                        │
       ┌────────────────┴────────────────┐
       ▼ (Parallel Execution, Serial: 20) ▼
 [Spirent VM 001] ...             [Spirent VM 132]
       │ 
       ├─► 1. Validate Netplan Syntax Config Pre-flight
       ├─► 2. Apply Custom IP Routing Tables via Netplan
       ├─► 3. Deploy & Bind Illumio VEN Grid
       └─► 4. Bulk Install: SentinelOne, Splunk, Nessus, Nagios
```

### 1. Defensive Netplan & Routing Automation
To safely alter the core networking layer of 132 live remote hosts without causing split-brain dropouts, the playbook implemented a defensive execution pipeline:
* **Staged Templating:** Utilized Ansible `template` modules to generate precise, valid YAML configurations inside a staging directory (`/etc/netplan/.stage/`), keeping existing production files completely untouched during transit.
* **Automated Linting & Pre-flight:** Executed remote validation checks (`netplan try --timeout 45`) via the automation engine. This ensured that if a routing configuration caused a drop in connectivity, the host automatically rolled back to its last known good network state.
* **Policy-Based Routing:** Successfully programmatically mapped explicit route tables, gateways, and interface bindings required to segregate Illumio management traffic from high-throughput Spirent simulation traffic.

### 2. Fleet Concurrency Tuning
* **Resource Isolation:** Spirent Landslide VMs process live telecom traffic. To prevent hypervisor CPU saturation or network bottlenecks during agent pushes, execution was restricted using Ansible's `serial: 20` rolling wave standard.

## 🚀 Business Impact & Results
* **Flawless Network Execution:** Successfully configured Netplan routing and deployed the 5-agent compliance matrix across all 132 machines with **0% network dropouts or manual rescue interventions**.
* **Rapid Lifecycle Compression:** Transitioned a highly complex networking and provisioning workflow from a projected 40+ hours of risky manual file editing to a fully automated execution completed in under an hour.
* **Defensive Baseline Architecture:** Established a robust, repeatable blueprint for deploying foundational network changes over Ansible without risking catastrophic remote node disconnection.
