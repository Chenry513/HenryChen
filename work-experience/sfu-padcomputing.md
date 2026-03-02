---
layout: page
title: "Research Assistant — SFU PadComputing Lab"
permalink: /work-experience/sfu-padcomputing/
---

**Aug 2024 – Aug 2025 · Burnaby, BC**
*Prof. Nicholas Vincent*

---

## The Project

[DL Tools](https://dl-tools.onrender.com) is a web platform built to make AI model information accessible to people who are not developers. Instead of digging through technical documentation to figure out how a model like ChatGPT or Claude handles your data, you can just look it up on the site and get a clear, plain-language breakdown. The platform covers things like what data each model trains on, privacy policies, and who runs it.

The content is community driven. Anyone can contribute by submitting a Markdown file to the GitHub repo and the system handles the rest automatically.

---

## First Term — Backend (Aug – Dec 2024)

When I joined the project the platform was early stage and needed a solid backend foundation. I built out the Django backend from scratch, which involved designing the relational schema for storing model metadata, implementing over eight RESTful API endpoints using Django REST Framework, and setting up migrations to keep the database in a stable state as the project evolved. I also worked on the Django Admin interface to make it easier to manage content without needing to touch the database directly.

The goal for this term was mostly getting the infrastructure in place so the platform could actually function reliably and be extended without things breaking.

---

## Second Term — Automation and CI (May – Aug 2025)

The second term was about making the platform easier to scale. The problem was that adding a new AI model to the site required someone to manually touch the database, which meant every content update needed a developer. That was not going to work long term.

I built an automated ingestion pipeline that takes a contributor's Markdown file, validates it against a schema to make sure the format and required fields are correct, converts it into Django models, and pushes it to the production database. The whole thing runs without anyone having to do anything manually. If the file passes validation it shows up on the live site within a minute of being submitted.

On top of that I integrated GitHub Actions to run the validation checks automatically on every submission, so bad or malformed files get caught before they ever reach the database. This cut down manual review work by around 60%.

The Markdown processing system is also built to be reusable. Any Django project that needs user-contributed structured content could plug this same pipeline in without forcing contributors to understand databases or backend code.

---

## Stack

- **Django / Django REST Framework** — backend, API, admin tooling
- **PostgreSQL** — database
- **Vue.js** — frontend
- **GitHub Actions** — automated validation and content pipeline
- **Render.com** — deployment
