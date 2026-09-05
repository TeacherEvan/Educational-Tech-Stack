# Educational Tech Stack

A focused learning repository covering **The Blazingly Fast Tech Stack**
introduced in [The Coding Sloth's tutorial video](https://www.youtube.com/watch?v=gFWZM0saGGI).
The curriculum teaches how to build modern full-stack applications by combining
**shadcn/ui**, **Clerk**, and **Convex** — the three tools at the heart of the
video's recommended stack.

## What You Will Learn

The materials walk through a deliberately small set of technologies, each
chosen for the leverage it adds on top of Next.js and TypeScript. shadcn/ui
gives you a component-driven, accessible React library that you actually own
the source of. Clerk replaces months of authentication plumbing with a hosted
identity service that already understands organizations, roles, and webhooks.
Convex replaces your REST + database + cache stack with a single reactive
backend where queries, mutations, and the database are one TypeScript
codebase. The exercise set drills each piece in isolation before combining
them in the capstone **PVT Class Tracker** project.

## Repository Layout

The repository is organised as a curriculum, not as an application. The
`modules/` directory holds the core module that follows the video from start
to finish. The `exercises/` directory contains the guided practice work — a
small Python refresher set under `exercises/python-basics/`, a single
HTML/CSS/JavaScript dashboard under `exercises/web-basics/`, and the
full-stack capstone under `exercises/full-stack-projects/`. Supporting
material — the programme summary, prerequisites, audit history, and setup
guide — lives at the repository root. The `scripts/` directory holds small
utilities such as the YouTube transcript fetcher used to derive exercises from
the source video.

## Getting Started

Read `curriculum-overview.md` for the full learning path, then follow the
setup instructions in `setup-guide.md` and `prerequisites.md`. The core
module under `modules/01-programming-fundamentals/` is the recommended
starting point; the `exercises/full-stack-projects/exercise-04-pvt-class-tracker.md`
specification is the multi-week capstone you build toward.
