---
layout: post
title: "Resume as a Build Artifact: Designing an Automated Resume SSoT Pipeline"
date: 2026-07-24
author: "Joshua Sternadel"
categories: [DevOps, CI-CD, Automation, SecOps]
tags: [github-actions, gradle, markdown, automation, pandoc, security]
---

## The Problem: I Was Fighting Microsoft Word
This project started with a resume template I received after being laid off from T-Mobile. The template was supposed to make resume creation easier. Instead, it became another thing I had to fight.

Then I started using AI to help write and refine the resume, and the proprietary Word template became even more of a problem. Formatting kept getting in the way of the actual work, so I eventually stripped the template out.

That created a different problem: getting reliable files into Applicant Tracking Systems (ATS).

I also ran into a problem with ATS processing. I was generating PDFs by using Ctrl+P and selecting Microsoft's PDF printer. The resulting PDF could contain artifacts that weren't present when using Word's built-in File → Export → PDF workflow. I didn't initially realize that these were two different document-generation paths, so I could make a change in Word, print it to PDF, and end up with an artifact that an ATS didn't interpret the way I expected.

That was the point where manually managing the conversion process started looking fundamentally stupid. If the PDF is a derived artifact, why was I manually deciding which conversion mechanism produced it every time?

Eventually, after a sufficiently large rant about why everything sucked, I asked a much simpler question:

> What if I just used Markdown and built the formats I need from that?

I didn't have a pipeline architecture in mind. I didn't even know Pandoc or WeasyPrint existed. I was initially thinking I might have to dust off Prince XML and write a PHP application to generate the documents. Then Gemini produced a GitHub Actions workflow that looked like Bash and YAML had an angry baby.

It was horrible.

But it proved something important:

The idea was possible.

At that point, the problem was no longer whether I could automate the process. It was figuring out how to build it properly.

**And this page is itself a build artifact.** The case study you're reading was generated and published by the same automated workflow described here. The case study's Markdown source is maintained alongside the project, transformed as part of the build, and deployed to the public GitHub Pages site. In other words, this page isn't documentation about the system sitting beside the system—it's one of the system's outputs.

---

## The Core Idea: One Source, Multiple Artifacts
The fundamental requirement was simple:

> I wanted one document that I maintained and multiple documents that I could distribute.

Markdown became the source of truth.

From that source, the system needed to produce the formats required for applications and publishing:

```
resume.md
    │
    ├──► PDF
    ├──► DOCX
    ├──► TXT
    └──► Web
```

The important part was that these files were no longer independently maintained documents.

They were build artifacts.

Change the source and rebuild.

That eliminated an entire class of problems caused by maintaining multiple representations of the same information manually.

---

### Why Gradle?
The first proof of concept demonstrated that GitHub Actions could execute the necessary commands, but I didn't want the build logic buried inside a GitHub Actions YAML file.

I wanted a deterministic build system.

GitHub Actions should answer:

> When and where does the build run?

The build system should answer:

> What does the build actually do?

That became the division of responsibility:

```
Git push
   │
   ▼
GitHub Actions
   │
   ▼
Gradle
   │
   ├── sanitize
   ├── generate documents
   ├── generate private variants
   └── generate web assets
```

Gradle became the place where the build logic lived, rather than treating the CI runner itself as the application.

The final build evolved iteratively. It wasn't a grand architecture designed before writing the first line of code. Requirements accumulated, experiments became build tasks, and the system grew as I discovered what the workflow actually needed.

---

## The Public/Private Boundary
At some point I decided that the resume should also be publicly available.

That introduced an entirely new requirement:

The resume I publish publicly cannot contain my private contact information.

My design principle was simple:

> DTA — Don't Trust Anyone.

I don't want the security of the public deployment to depend on me remembering to remove a phone number, email address, or other private information before every build.

So the private and public builds are deliberately separate.

```
                  PRIVATE SOURCE
                       │
              ┌────────┴────────┐
              │                 │
              ▼                 ▼
       PRIVATE BUILD        SANITIZE
              │                 │
              ▼                 ▼
     Private PDF/DOCX/TXT   PUBLIC SOURCE
                                │
                                ▼
                           PUBLIC BUILD
                                │
                    ┌───────────┼───────────┐
                    ▼           ▼           ▼
                   PDF         DOCX        TXT
                                │
                                ▼
                           PUBLIC WEBSITE
```

The important security property is not merely that PII gets removed. It is that the public build never needs to consume the private source.

---

## Sanitize Once, Then Build
The sanitization task transforms the private Markdown source into a separate public Markdown source.

That sanitized source then becomes the input to every public artifact.

```
Private Markdown
      │
      ▼
Sanitization
      │
      ▼
Sanitized Markdown
      │
      ├──► PDF
      ├──► DOCX
      ├──► TXT
      └──► Web
```

This creates a simple invariant:

> If the public source is sanitized, every successfully generated artifact from that source is sanitized.

I don't have to independently sanitize a PDF, then sanitize a DOCX, then sanitize a text file, then remember to sanitize the web version. The security transformation happens once, at the boundary between private and public data. Everything downstream consumes the sanitized representation. That is considerably easier to reason about than trying to remove sensitive information from every possible output format.

---

## An Accidental Test of the Security Boundary
The separation also proved useful in an unexpected way. At one point I accidentally overwrote private output with sanitized documents. The result wasn't a privacy breach. It was the opposite: I sent several resumes out without my contact information.

Nobody got my private data. They just got a resume that effectively said:

> Contact via LinkedIn.

I didn't hear back from those people.

It was annoying, but it demonstrated an important property of the architecture: 

The dangerous failure mode was contained in the safer direction.

The system could fail by producing a sanitized document where a private document was expected. It should not be able to accidentally turn the private source into a public artifact containing private information.

---

## Build Orchestration
The Gradle build eventually grew into several distinct responsibilities.

### Public Document Sanitization

The sanitization task:
- reads the private Markdown source;
- removes the explicitly defined private contact information;
- applies transformations required by the public publishing environment;
- writes the sanitized Markdown into the public build area;
- distributes the sanitized source to the public repository.

The sanitized Markdown is therefore itself a build artifact and the input to subsequent public compilation.

### Public Document Generation

The document-generation task depends on sanitization.

Pandoc converts the sanitized Markdown into the required distribution formats:
- PDF through WeasyPrint;
- DOCX using a reference document;
- plain text for systems that require it;
- optional HTML output for debugging.

The important dependency is:

```
sanitize
    ↓
generate documents
```

rather than allowing document generation to bypass the sanitization stage.

### Private Document Generation

Private resume variations are generated directly from the raw Markdown source. The build discovers the source files matching the expected resume naming convention and generates corresponding private PDF, DOCX, and TXT artifacts. This allows multiple application-specific resume variations to exist without requiring the same formatting work to be repeated manually.

### Web Asset Generation

The web build also has its own generated assets. The pipeline takes the site's CSS and produces the SCSS asset expected by the Jekyll-based public site, keeping the web representation tied to the same build process.

---

## The Result
What started as:

> I am sick of fighting Microsoft Word.

became a source-controlled document compilation system.

The resume itself became the source.

Everything else became an artifact.

```
                 Single Source of Truth
                         │
                         ▼
                    Markdown
                         │
              ┌──────────┴──────────┐
              │                     │
              ▼                     ▼
       Private Build          Sanitization
              │                     │
              ▼                     ▼
    Private Artifacts        Public Source
                                    │
                                    ▼
                              Public Build
                                    │
                         ┌──────────┼──────────┐
                         ▼          ▼          ▼
                        PDF        DOCX       TXT
                                               │
                                               ▼
                                         GitHub Pages
```

A resume change is now a source change.

A source change can be rebuilt deterministically. The resulting artifacts don't have to be manually synchronized. And the public build has a defined security boundary between private source material and publicly distributed artifacts. The final system was not designed all at once. It emerged through several iterations as new problems appeared.

The initial requirement was simply:

> I need the output files.

Everything else grew from there.

And somewhere around the third hyperfixation loop, I realized I'd accidentally built a resume compiler.