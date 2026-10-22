# Deploying Kortix, the open-source Open WebUI alternative

Kortix is the open-source AI Operating System, and deploying it to replace a chat-only interface takes one Docker Compose stack plus a managed sandbox provider. The sequence is a short init flow and a few settings in the dashboard, and it runs on a box you control.

If you are still deciding whether to move off Open WebUI at all, start with [Kortix vs Open WebUI](kortix-vs-open-web-ui.md). The steps that follow assume you decided, and they cover the commands.

## What you are deploying

A self-hosted Kortix instance is one generic Docker Compose project. It runs the frontend, the API, the LLM gateway, and the official Supabase distribution, with a bundled Caddy proxy in front ([self-hosting architecture](https://kortix.com/docs/host/architecture)). The same artifact runs on a laptop, a VPS, or any cloud VM; a domain is only the `KORTIX_DOMAIN` variable, not a different setup.

Agent sessions do not run on this box. Each session boots its own isolated cloud computer on a managed sandbox provider, and the provider defaults to Platinum. Daytona remains available as a legacy fallback. Sandbox compute never runs on the self-host box, so the box only needs capacity for the web stack.

## Prerequisites

You need a Linux box you control and a domain. The documented floor is 2 vCPU and 4 GB of RAM; 4 vCPU and 16 GB or more is recommended for real use. Ports 80 and 443 must be reachable from the internet, because the Caddy proxy uses ACME HTTP-01 to issue a TLS certificate.

One domain detail matters after setup: without a persistent domain or a tunnel, sessions cannot run, because the sandbox has no stable URL to reach the API back at. Plan for a real domain, or use the evaluation mode below.

## Step 1: point DNS at the box

Create an A or AAAA record for your domain, and one for `api.<domain>`, both pointing at the box's public IP. The API subdomain is the default and is set by `KORTIX_API_DOMAIN`.

## Step 2: install the CLI

Install the `kortix` CLI on the box:

```sh
curl -fsSL https://kortix.com/install | bash
```

The self-hosting guide also documents a one-shot bootstrap script that installs Docker if it is missing, installs the CLI, and runs the same `init` and `start` flow. Re-running it is safe because every step is idempotent ([self-hosting docs](https://kortix.com/docs/host)).

## Step 3: initialize and start

Initialize the instance, then bring the stack up:

```sh
kortix self-host init --domain kortix.example.com
kortix self-host start
```

`init` asks six things and no model key: the domain or tunnel, the admin email, the deployment shape, the sandbox provider and its API key, an optional Pipedream connector setup, and the update policy. Use `kortix self-host status`, `kortix self-host logs`, and `kortix self-host doctor` while the stack comes up.

The compose file in this repo is the same topology in a readable form. `kortix self-host init` renders the complete, digest-pinned version plus a `.env`. Every secret in the template is a placeholder; put real values in `.env` or the project secret store.

## Evaluation mode without a domain

To try Kortix with no domain, use a Cloudflare tunnel instead:

```sh
kortix self-host init --tunnel cloudflare
kortix self-host start
```

The tunnel URL changes on every restart, so use this mode for evaluation rather than production. A fully local instance is also possible with neither a domain nor a tunnel, though no agent sessions will run.

## Step 4: connect a sandbox provider

After the stack starts, set the provider key:

```sh
kortix self-host configure
```

`configure` prompts for the sandbox provider key and, optionally, a managed-git token. Sign up in the dashboard with the admin email from init, then connect your own model key in the model picker. Self-hosted instances use your own key by default.

Until email is configured, sign-up is open and needs no confirmation, so anyone who reaches the URL can create an account. Sign up first, then close it with `kortix self-host env set EMAIL_URL=smtp://...` or turn self sign-up off with `kortix self-host env set DISABLE_SIGNUP=true` ([self-hosting docs](https://kortix.com/docs/host)).

In the dashboard, finish two settings:

- Settings then Git: connect a GitHub App or a personal access token so the platform can create project repos.
- Settings then Model: connect a model provider key.

## Step 5: ship your project

From here the CLI works against your instance the way it works against Kortix Cloud:

```sh
kortix hosts use selfhost
kortix login
kortix projects ls
cd your-project && kortix ship
```

Copy the `kortix.yaml` from this repo into your project at the repo root, or keep it and edit the agent and connector names. The manifest sets governance: `agents`, `connectors`, secret names, and `triggers`. Behavior such as the system prompt and model lives in each `agents/<name>.md` file. `kortix validate` runs the same manifest check as `kortix ship` and the change request merge.

## Updates and backups

Every instance updates itself. The in-compose updater checks for a new image once a day and runs a start-first swap; a domain-mode box with two replicas is zero-downtime. The stack tracks the `stable` channel by default, or `latest`, and you can pin an exact version:

```sh
kortix self-host update --tag 0.9.84
```

Turn the updater off with `--auto-update off` on init, or manage it later.

Kortix has no separate backup system. Each instance stores its data as two directories under `~/.config/kortix/self-host/<instance>/`: `volumes/db/data` for Postgres and `volumes/storage` for file storage. The instance `.env` holds every secret and signing key. Back up all three before a destructive command.

## Common first-run issues

Sessions fail to start when no sandbox provider key is set. Run `kortix self-host configure` and set `DAYTONA_API_KEY`, or the key for whichever provider you chose.

TLS never issues when ports 80 and 443 are closed or DNS has not propagated. Check both records resolve to the box, then rerun `kortix self-host doctor`.

The API container hits its memory limit on a busy host. Each API container defaults to a 640 MiB limit, tuned for an 8 GB host. On a 16 GB host, raise it with `kortix self-host env set KORTIX_API_MEMORY_LIMIT=1024m` and confirm with `docker stats --no-stream`.
