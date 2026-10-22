# FAQ: Kortix, the open-source Open WebUI alternative

Kortix is the open-source AI Operating System, and these are the questions teams ask before they move from a chat UI to it. Each answer stands on its own.

## Is Kortix open source?

Yes. Kortix is open source: read it, fork it, audit it. The platform, the CLI, and the harness are public, and each project you run on Kortix is your own git repository. You keep the configuration, the memory, and the connectors as files you own ([Kortix on GitHub](https://github.com/kortix-ai/suna)).

## How is Kortix different from Open WebUI?

Open WebUI is a self-hosted AI platform built to run entirely offline, and it is a strong chat and retrieval surface over your models and documents ([open-webui/open-webui](https://github.com/open-webui/open-webui)). Kortix is an AI Operating System: agents run on their own cloud computers, use connectors, and return finished work, and the whole configuration lives in a git repo you own. One returns answers in a chat; the other returns deliverables and lands changes through a review.

## Do I need a domain to self-host Kortix?

For production, yes. A persistent domain gives the Caddy proxy a stable name for TLS and gives agent sandboxes a stable URL to call the API back at. Without a domain or a tunnel, sessions cannot run. For evaluation you can use a Cloudflare tunnel with `kortix self-host init --tunnel cloudflare`, but the tunnel URL changes on every restart, so it is not for production ([self-hosting docs](https://kortix.com/docs/host)).

## Where does agent compute run?

Agent sessions run on a managed sandbox provider, not on the self-host box. Platinum is the default; Daytona remains available as a legacy fallback. The self-host box runs the web stack, and sandbox compute stays off it, so the box only needs capacity for the application ([self-hosting architecture](https://kortix.com/docs/host/architecture)).

## Can I bring my own model?

Yes, any model, your own keys. Kortix is model-agnostic: pick the model per agent, per session, or per message, and bring an API key from any major provider. You can also connect the ChatGPT subscription you already pay for, or point at your own OpenAI-compatible endpoint. Self-hosted instances use your own key by default.

## How do updates and backups work?

Every instance updates itself, or you pin an exact version with `kortix self-host update --tag <version>`. The updater checks once a day and does a start-first swap. Kortix has no separate backup system: back up the instance's `volumes/db/data`, `volumes/storage`, and `.env` before any destructive command ([self-hosting docs](https://kortix.com/docs/host)).

## Where do I go next?

Follow [Deploying an Open WebUI alternative](deploying-an-open-web-ui-alternative.md) for the full sequence, or read [Kortix vs Open WebUI](kortix-vs-open-web-ui.md) if you are still comparing. The companion site's [self-hosting page](https://openwebuialternative.com/self-hosting) collects the deployment options, and the [Open WebUI alternative home page](https://openwebuialternative.com/) gives the wider overview.
