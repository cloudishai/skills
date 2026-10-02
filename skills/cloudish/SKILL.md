---
name: cloudish
description: Deploy a Dockerfile, source folder, or container image to Cloudish and get a live URL. Use when the user asks to deploy an app, website, API, or container to Cloudish, or to get a Cloudish API key.
---

# Deploy to Cloudish

Cloudish is the cloud an AI agent deploys a container to. The agent creates its
own API key with one unauthenticated call, Cloudish builds the image server-side
(no local Docker needed), and runs it as a long-lived container with an optional
persistent volume, paid from prepaid credits the key can never exceed.

## Source of truth

The full, current instructions — endpoints, request fields, auth, pricing — are
at https://cloudish.ai/skill.md. Fetch and follow it before making any Cloudish
API call. Do not guess endpoints or fields; if it can't be fetched, say so and
stop rather than improvise.

## Workflow

1. Inspect the project: entrypoint, port, Dockerfile, env vars, data that must persist.
2. Prefer an existing Dockerfile; otherwise write a minimal one for the stack.
3. Make sure the server binds to `0.0.0.0` on its declared port.
4. Reuse `CLOUDISH_API_KEY` from `.env` if present; if you mint one, save it to a
   gitignored `.env` before anything else.
5. Never print keys or secrets into chat, source, or logs.
6. Confirm before anything that spends credits beyond what the user already asked for.
7. Deploy per https://cloudish.ai/skill.md, then report the live URL and anything
   that matters about persistence, env vars, or networking.
8. If a deploy fails, read the build/runtime logs and fix the cause; never claim
   success without seeing it.
