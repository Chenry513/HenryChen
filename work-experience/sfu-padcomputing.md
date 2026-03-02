---
layout: page
title: "Research Assistant - SFU PadComputing Lab"
permalink: /HenryChen/work-experience/sfu-padcomputing/
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

I built an automated ingestion pipeline that takes a contributor's Markdown file, validates it against a schema to make sure the format and required fields are correct, converts it into Django models, and pushes it to the SQLite database. The whole thing runs without anyone having to do anything manually. If the file passes validation it shows up on the live site within a minute of being submitted.

On top of that I integrated GitHub Actions to run the validation checks automatically on every submission, so bad or malformed files get caught before they ever reach the database. This cut down manual review work by around 60%.

---

## The Linter

The core of the validation system is a custom Python linter called `md-data-lint`. It takes a directory of Markdown files and a JSON schema definition, then checks every file to make sure the frontmatter is valid before anything touches the database.

Here is what it does step by step:

```python
linter = MdDataLint("example-schema.json")
results = linter.validate_directory("markdown_files/")
```

It loads the schema, finds all `.md` files in the directory, and for each file it extracts the YAML frontmatter, checks that all required fields are present, flags any unknown fields as warnings, and validates field types using Pydantic. At the end it prints a summary table showing which files passed and which failed, and exits with a non-zero status code if anything is invalid so GitHub Actions knows to block the commit.

A valid contributor file looks something like this:

```yaml
---
name: ChatGPT
organization: OpenAI
use_cases:
  - Text generation
  - Code assistance
severity: medium
---
```

If a required field is missing or a field has the wrong type, the linter catches it and reports exactly which file and which field failed before anything reaches the database.

---

## The Processing Pipeline

Once validation passes, a Django management command handles the actual database update. It runs automatically after every Markdown commit via GitHub Actions.

```python
# Validate all files and get the list of valid ones
linter = MdDataLint(schema_path)
results = linter.validate_directory(markdown_dir)
valid_names = [os.path.basename(r.file_path)[:-3] for r in results if r.is_valid]

# Remove any database records that no longer have a corresponding file
stale_records = ModelInfo.objects.exclude(name__in=valid_names)
stale_records.delete()

# Upsert each valid file into the database
for result in results:
    if result.is_valid:
        process_file(result.file_path)
```

The key part is `update_or_create` — if a model already exists in the database it gets updated, if it is new it gets inserted. It also cleans up stale records automatically, so if someone deletes a Markdown file the corresponding database entry gets removed too. No manual intervention needed at any point in the flow.

---

## Stack

- **Django / Django REST Framework** — backend, API, admin tooling
- **SQLite** — database
- **Vue.js** — frontend
- **GitHub Actions** — automated validation and content pipeline
- **Render.com** — deployment
