---
layout: page
title: Rankr
permalink: /projects/rankr/
---

**July 2024 · Real-Time Web App**
[github.com/Chenry513/rankr](https://github.com/Chenry513/rankr)

## The Project

Rankr is a real-time polling app for group decision making. You create a session, share the poll ID with whoever needs to join, everyone nominates options and submits their rankings, and the final result gets computed and displayed live as votes come in. No page refreshes, no waiting.

## How It Works

The host creates a poll and gets a shareable ID. Participants join using that ID, nominate their options once the session opens, then rank everything once voting starts. All of that state is synchronised in real time via Socket.io so every participant sees updates the moment they happen. Redis stores the session and vote state since poll data is ephemeral and gets read and written frequently by multiple people at the same time.

```
Host creates poll → Shareable Poll ID → Participants join →
Everyone nominates → Voting opens → Rankings submitted → Results displayed live
```

## Why Socket.io and Redis

HTTP polling would introduce noticeable lag for something that is supposed to feel collaborative and instant. Socket.io gives true bidirectional communication so state changes propagate immediately to everyone in the session.

Redis handles the session storage because poll data does not need to persist long term and gets accessed constantly during a live session. It handles that read/write pattern with far lower latency than a relational database, and RedisJSON lets us store structured poll state without serialization overhead.

## Stack

- **React / TypeScript** — frontend
- **Vite / Tailwind CSS** — build tooling and styling
- **Node.js / Socket.io** — backend and real-time communication
- **Redis / RedisJSON** — session and vote state storage
- **Docker Compose** — containerised local development
