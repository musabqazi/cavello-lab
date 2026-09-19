<div align="center"><img src="cover.png" width="100%"></div>

**[← All systems](https://github.com/musabqazi)** · [Hook Lab](https://github.com/musabqazi/hook-lab) · [Caption Lab](https://github.com/musabqazi/caption-lab) · [Portfolio](https://github.com/musabqazi/portfolio)

# Carousel Lab

**Transcript in, finished on-brand Instagram carousel out, in about two minutes — with a human approving every one before it can be exported.**

🟢 **In production** · **Client:** content agency (anonymised) · **Source:** private, available on request

## The problem

A content agency turns long-form video into carousels for a roster of creators. Doing it by hand takes a designer an hour per carousel and the output drifts — the cover type creeps a few pixels, the highlight colour is *nearly* right, one creator's voice bleeds into another's. Handing the whole job to a model makes drift worse, not better: ask an LLM to lay out a slide and you get a different layout every run.

## What I built

- **A split that makes drift impossible.** Models return structured *decisions* only — copy, hook classification, emphasis spans, image choice, layout variant. A deterministic template engine turns those decisions into pixels. The cover type is exactly 46px and the body exactly 33.4px because code sets them, not because a model was asked nicely.
- **A vision pass over the image library.** Every uploaded photo is described, tagged and scored before it becomes selectable, so slide image choice is a lookup over indexed evidence rather than a guess.
- **Per-creator scoping enforced in the database**, not the UI. A creator's rules, handle and library reach a run only when that creator is selected — a trigger on the auth table enforces the domain allowlist for OTP, magic link, password and direct API calls alike.
- **A human gate before export.** Nothing leaves the system unapproved. The review editor also works the other way round: write the copy by hand from the first line and let the engine render it.
- **Reproducible output.** Same input, same pixels. Over 40 scripted checks each prove one property against the real system rather than a mock.

## Screenshots

<img src="screenshots/01-generate.png" alt="Generate: pick the creator, drop the transcript, and the render settings the engine will hold to" width="100%"/>
<sub>Generate — the creator's render contract is visible before anything runs: cover type, body type, highlight colour, handle, slide range.</sub>

<table>
  <tr>
    <td width="50%" valign="top"><img src="screenshots/02-review-queue.png" alt="Review queue: every carousel waits for human approval before export" width="100%"/><br/><sub>Review queue — nothing exports unapproved.</sub></td>
    <td width="50%" valign="top"><img src="screenshots/03-library-vision-pass.png" alt="Image library: every upload described, tagged and scored by the vision pass" width="100%"/><br/><sub>Library — each photo scored by the vision pass before it can be selected.</sub></td>
  </tr>
</table>

<img src="screenshots/04-highlight-render.png" alt="Close-up of the deterministic highlight and type rendering" width="100%"/>
<sub>The emphasis span, rendered by the template engine — the model chose <em>which</em> words, the code chose every pixel.</sub>

## Architecture

```mermaid
flowchart LR
    T["Transcript"] --> P["Parser<br/>timestamps · speaker labels"]
    P --> C["Copy agent<br/>structured decisions only"]
    C --> H["Hook classifier<br/>archetype · emphasis spans"]
    H --> S["Image selector"]
    L[("Image library<br/>vision-scored")] --> S
    S --> R["Template engine<br/>deterministic render"]
    R --> G["Quality gates<br/>40+ scripted checks"]
    G --> Q["Review queue<br/>human approval"]
    Q --> E["Export"]
    DB[("Postgres / Supabase<br/>per-creator scoping")] --- C
    DB --- L
    DB --- Q
    W["BullMQ worker<br/>Redis"] --- R
```

## Stack

![Next.js](https://img.shields.io/badge/Next.js_15-000000?style=flat-square&logo=nextdotjs&logoColor=white)
![React](https://img.shields.io/badge/React_19-61DAFB?style=flat-square&logo=react&logoColor=black)
![TypeScript](https://img.shields.io/badge/TypeScript_5-3178C6?style=flat-square&logo=typescript&logoColor=white)
![Supabase](https://img.shields.io/badge/Supabase-3FCF8E?style=flat-square&logo=supabase&logoColor=white)
![Postgres](https://img.shields.io/badge/Postgres-4169E1?style=flat-square&logo=postgresql&logoColor=white)
![BullMQ](https://img.shields.io/badge/BullMQ_+_Redis-DC382D?style=flat-square&logo=redis&logoColor=white)
![Gemini](https://img.shields.io/badge/Google_Gemini-4285F4?style=flat-square&logo=google&logoColor=white)
![Sharp](https://img.shields.io/badge/Sharp-99CC00?style=flat-square)
![Tailwind](https://img.shields.io/badge/Tailwind_4-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white)
![Zod](https://img.shields.io/badge/Zod-3E67B1?style=flat-square&logo=zod&logoColor=white)

## My role

Architecture, the template engine, the gate system, the BullMQ worker, the image library tooling and vision pass, the review editor, the per-creator scoping model, and the VPS deployment.

## Outcomes

- Carousel turnaround went from about an hour of designer time to roughly two minutes of machine time plus a human approval.
- Output is reproducible run to run — the property the deterministic renderer exists to guarantee, and the one the check suite proves.
- One creator's rules cannot reach another creator's run; scoping is enforced at the database, not in the interface.

## A note on what you can see here

Screenshots use seeded demo data and pseudonymised creator names. Real client content, transcripts and caption libraries are not published. The source is private — happy to walk through it.

---
<sub>Part of the <a href="https://github.com/musabqazi">musabqazi portfolio</a> — real systems, anonymised data, source private. © 2026 Musab Qazi</sub>
