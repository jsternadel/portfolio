---
layout: post
title: "Automated Fleet Mobilization: Bootstrapping 132 Spirent VMs and Engineering Resilient Netplan Routing"
date: 2026-07-24
author: "Joshua Sternadel"
categories: [DevOps, Automation, Networking, Security]
tags: [ansible, netplan, illumio, security, networking, linux]
---

## I started with Google and prayer.

When this project began, I had essentially no Ansible experience. There was no training budget, no Ansible Galaxy access, and no existing automation framework to extend. I had access to Ansible Automation Platform, a browser, and a problem: 132 Spirent Landslide Ubuntu VMs needed to be onboarded to the organization's security and telemetry stack.

The easy part was installing software. The hard part was doing it without breaking the machines.

Illumio VEN required routing configuration that had to be added to the Spirent network topology. On Ubuntu, that meant Netplan. And everyone involved understood the obvious failure mode: get the YAML wrong and the remote VM could disappear from SSH.

### Nobody wanted to touch Netplan.

We had 132 Spirent Landslide VMs that needed Illumio VEN and several other security agents installed. The Illumio deployment required additional routing configuration so the VEN could reach its management infrastructure through our core network. On Ubuntu, that meant modifying Netplan.

The problem was simple to describe and terrifying to execute: break the YAML and you could lose SSH to the machine.

I had been using Ansible for about three months.