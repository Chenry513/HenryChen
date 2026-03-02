---
layout: page
title: readmeify
permalink: /projects/readmeify/
---

**Feb 2026 · Full-Stack Web App**
[readmeify-five.vercel.app](https://readmeify-five.vercel.app) · [github.com/Chenry513/Readmeify](https://github.com/Chenry513/Readmeify)

## The Problem

Writing READMEs is tedious and most AI-generated ones are useless because the model is just guessing what the project does. It produces generic boilerplate that could apply to any project with a similar name. The fix is to actually read the repo before generating anything.

## How It Works

You sign in with GitHub OAuth, pick a repo from the sidebar, and hit generate. Before anything goes to the AI, the app pulls your file tree and any dependency manifests it can find including `package.json`, `requirements.txt`, `Cargo.toml`, `go.mod`, and an existing README if there is one. All of that gets sent as structured context to the model so it knows your actual stack, scripts, and dependencies rather than just the repo name.

The output streams in token by token so you are not staring at a spinner waiting for a wall of text to appear. Once it is done you can review it in the preview tab or look at the raw markdown, then commit it directly to your repo in one click.

```
GitHub OAuth → Select Repo → Pull file tree + deps →
Groq API (LLaMA 3.3 70B) → Stream to UI → Commit to GitHub
```

## Why Groq Over OpenAI

Groq's inference speed is noticeably faster for streaming use cases. Output starts appearing almost instantly rather than waiting for the model to finish before anything shows up. LLaMA 3.3 70B produces solid structured markdown output for this kind of task and the latency difference is meaningful when you are watching it stream in real time.

## Security

All Groq and GitHub API calls happen server-side in Next.js API routes. The API keys never touch the client bundle so they cannot be exposed through browser devtools or bundle analysis. GitHub OAuth is handled through NextAuth which takes care of the full flow, session management, and token storage server-side.

## Stack

- **Next.js** (App Router) — framework
- **NextAuth.js** — GitHub OAuth and session management
- **Groq API / LLaMA 3.3 70B** — AI generation with streaming
- **GitHub API** — repo listing, file tree, dependency reading, commit creation
- **Vercel** — deployment
