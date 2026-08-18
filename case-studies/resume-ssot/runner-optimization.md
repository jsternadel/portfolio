---
layout: post
title: "Resume as a Build Artifact (part 2): Creating a Controlled Resume Build Environment"
date: 2026-07-24
author: "Joshua Sternadel"
categories: [DevOps, Containerization, CI-CD, Infrastructure]
tags: [docker, github-actions, runner, cicd, optimization, self-hosted]
---

The original GitHub Actions workflow ran directly on a GitHub-hosted Ubuntu 22.04 environment. Each build had to provision the tools required to process and render the resume: Java, Git, rsync, Pandoc, WeasyPrint, fonts, and the other supporting packages.

That meant the build itself depended on `apt` and the Ubuntu package mirrors being healthy.

Eventually, that dependency became a problem. APT was spending significant time retrying failed mirrors and falling back through other mirrors before it found one that would actually answer. The build could eventually succeed, but the behavior was inconsistent and the source of the delay had nothing to do with building the resume.

It was a single incident. As far as I knew, the mirrors had simply had a bad day. But that was enough. The build had demonstrated that an infrastructure failure outside of the project could prevent or materially disrupt a deployment. From my perspective, the build pipeline was compromised. Resilience had failed, and I didn't see a reason to accept that dependency when I could control the build environment myself.

I hate inefficiency. My brain does not respond well to a process that wastes time for no useful reason.

So I stopped provisioning the build environment during the build process.

---

## The Builder Image
I created a dedicated `resume-builder` container based on Ubuntu 22.04 and moved the environment dependencies into the image itself.

The image contains the tools the pipeline needs:
- OpenJDK 21
- Git
- rsync
- Pandoc
- WeasyPrint
- Noto fonts
- The Ubuntu environment those tools expect

The important part wasn't simply installing those packages once. As I troubleshot the new pipeline, whenever I discovered another runtime dependency, I put it in the builder image rather than adding another installation step to the deployment workflow.

> Why spend network time constructing an environment when the build only needs that environment to work?

The Dockerfile became the definition of the build environment. If I eventually need to pin a package version or change one of the dependencies, I control where that decision is made. The build no longer needs to ask APT to construct its environment before it can do its actual work.

---

## Making the Builder Part of the Pipeline
The deployment has an explicit prerequisite that ensures the image exists before the actual build begins:

```
push to main
    ↓
ensure builder image exists
    ↓
run build inside resume-builder
    ↓
generate artifacts
    ↓
deploy
```

Getting that dependency relationship into GitHub Actions was actually one of the more frustrating parts of the implementation. I was new to GitHub Actions, and getting an AI assistant to arrive at the ensure-image approach took several iterations.

I knew what I wanted the system to do. I didn't necessarily know the GitHub Actions mechanism for expressing it.

Once it was in place, the architecture was straightforward: the deployment does not have to assume that the builder image has already been created.

## The Tradeoff
The new pipeline introduced some overhead.

The image-check/build portion adds roughly 15–30 seconds to a normal deployment. I'm completely comfortable with that tradeoff. The important change is that build times are now remarkably consistent.

I traded a potentially faster build with unpredictable provisioning behavior for a slightly slower build with predictable behavior.

That is a worthwhile trade.

A build that takes roughly the same amount of time every time is much easier to reason about than one that is occasionally fast, occasionally slow because of package mirrors, and occasionally fails because infrastructure unrelated to the resume happened to be having a bad day.

## What I Actually Gained
The biggest win wasn't shaving seconds off the build.

It was control.

I control the builder image. I control what tools are installed in it. I can pin versions when I need to. I can add dependencies to the image when the build requires them. And the deployment no longer has to construct its environment while it is trying to perform the work I actually care about.

The result is not necessarily the fastest possible resume build.

It is a build that behaves consistently, runs in an environment I control, and doesn't require me to repeatedly deal with infrastructure problems that have nothing to do with the resume itself.

For me, that is reliability.

**Consistency is reliability.**