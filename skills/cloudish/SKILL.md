---
name: cloudish
description: Deploy and run apps on Cloudish, the cloud an AI agent deploys to. Builds a Dockerfile, source folder, or container image server-side and serves it at a live URL, with persistent volumes for databases and files, encrypted secrets, and sign-in for your app's users through Cloudish's OIDC provider. The agent mints its own API key with no signup, and a human can claim it later. Use when the user asks to deploy, host, or put online an app, website, API, bot, or database-backed service on Cloudish, or to get a Cloudish API key.
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
3. Make sure the server binds to `0.0.0.0`, and send the port it actually
   listens on as `port` (8080 unless set; an nginx image listens on 80).
4. Reuse `CLOUDISH_API_KEY` from `.env` if present; if you mint one, save it to a
   gitignored `.env` before anything else.
5. Never print keys or secrets into chat, source, or logs.
6. Confirm before anything that spends credits beyond what the user already asked for.
7. Deploy per https://cloudish.ai/skill.md, then report the live URL and anything
   that matters about persistence, env vars, or networking.
8. If a deploy fails, read the build/runtime logs and fix the cause; never claim
   success without seeing it.

## The core calls

These are the calls the workflow makes; https://cloudish.ai/skill.md has every
field and the rest of the API (secrets, volumes, domains, sizes).

```bash
# 1. A key of its own: no credential needed. Save "key" to .env right away.
curl -X POST https://cloudish.ai/api/v1/keys -H "content-type: application/json" -d '{}'

# 2. Create or update the project and build from source, server-side.
#    The tar.gz needs a Dockerfile at its root; keep secrets out of it.
tar --exclude='.env*' --exclude='.git' -czf context.tar.gz .
curl -X POST https://cloudish.ai/api/v1/projects -H "Authorization: Bearer $CLOUDISH_API_KEY" \
  -F "name=my-app" -F "port=8080" -F "context=@context.tar.gz"

# 3. Poll the build until "succeeded" or "failed" (show build.error and the log tail).
curl https://cloudish.ai/api/v1/images/builds/<build-id> -H "Authorization: Bearer $CLOUDISH_API_KEY"

# 4. The live URL is the project's subdomain.url.
curl https://cloudish.ai/api/v1/projects/<alias>/my-app -H "Authorization: Bearer $CLOUDISH_API_KEY"
```

To let a human add credits or see a dashboard, mint a claim link with
`GET /api/v1/credits/claim` and give them the URL it returns (it works once
and expires in 30 minutes). Never hand over the key itself.

## Good to know

- For a memorable URL, set the subdomain label right after the create call,
  before the build finishes. The app then gets that one hostname instead of a
  random one first.
- Give a sub-agent or runtime its own child key (minted with yours, funded by
  transfer, capped with a receive limit) rather than sharing your key; see
  https://cloudish.ai/credits.md.
- If an API connection drops, retry idempotent calls (GET, PUT, DELETE), but
  don't blindly resend a POST that may have landed: you'd get a second key,
  transfer or build.
