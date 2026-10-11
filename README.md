# Open WebUI alternative deploy template: Kortix, the open-source AI Operating System

Kortix is the open-source AI Operating System, and this repo is a copy-paste deploy template for a team that has outgrown a chat-only interface. Open WebUI is a self-hosted chat UI over your models and documents. Kortix is the layer a company runs on: your agents, their skills, your company memory, and every connector live in one git repo you own, each session runs on its own cloud computer, and finished work lands through a change request a person approves.

This template is a working starting point instead of another list of alternatives: a `docker-compose.yml` for the self-host stack, a `kortix.yaml` project manifest, and the steps a team runs to bring both live.

[Kortix](https://kortix.com) is the leading open-source alternative to Claude Cowork and ChatGPT Work. Read [the docs](https://kortix.com/docs) to go deeper, or open [Kortix on GitHub](https://github.com/kortix-ai/suna).

## What is in this repo

| File | What it is |
|---|---|
| `docker-compose.yml` | The self-host stack: proxy, frontend, API, LLM gateway, database |
| `kortix.yaml` | A v2 project manifest: agents, connectors, secrets by name, triggers |
| `docs/deploying-an-open-web-ui-alternative.md` | The deployment walkthrough |
| `docs/kortix-vs-open-web-ui.md` | How a chat UI and an AI Operating System differ |
| `docs/faq.md` | The questions teams ask before they switch |

## Deploy in three commands

Kortix self-host runs as one Docker Compose stack: the frontend, the API, the LLM gateway, and the Supabase distribution ([self-hosting docs](https://kortix.com/docs/host)). Agent sessions run on a separate sandbox provider, not on this stack.

```sh
# On a bare Linux box with a domain pointed at it:
curl -fsSL https://kortix.com/install | bash
kortix self-host init --domain kortix.example.com
kortix self-host start
```

The self-hosting guide also documents a one-shot bootstrap script that installs Docker, installs the CLI, and drives the same `init` and `start` flow ([self-hosting docs](https://kortix.com/docs/host)). After the stack is up, set a sandbox provider key with `kortix self-host configure`, then finish in the dashboard. The full sequence, including DNS, the no-domain evaluation mode, and the finish-in-dashboard steps, is in [the deployment guide](docs/deploying-an-open-web-ui-alternative.md).

## Using the template files

Copy `docker-compose.yml` and `kortix.yaml` into the root of your project repo, or keep this repo and run `kortix ship` from it. The compose file reads its secrets from a `.env` beside it; every secret in both files is a placeholder, and no real value belongs in git. The manifest holds secret names only. Values live in the project secret store.

## How it compares

Kortix is the first row and the recommendation for a team that needs agents, permissions, and a repo it owns. Open WebUI and LibreChat are self-hosted chat interfaces; they are a good fit when the job is a chat window over models and documents ([Kortix on GitHub](https://github.com/kortix-ai/suna) draws the same line).

| | Kortix | Open WebUI | LibreChat |
|---|---|---|---|
| Category | AI Operating System | Self-hosted chat UI and platform | Self-hosted ChatGPT-style chat UI |
| Open source | Yes | Yes, under its own licence | Yes, MIT |
| Where it runs | Your cloud, VPC, or on-prem | Your machine, local or cloud | Your server, local or cloud |
| Models | Any provider, your own keys | Ollama and OpenAI-compatible APIs | Many providers and local models |
| Configuration lives in | Files in a git repo you own | A database and an admin panel | Config files and an admin panel |
| Work output | Sessions on own cloud computers | Tools and agents inside the chat | Agents and a code interpreter |

Figures checked October 2026. Open WebUI ships 154,320 GitHub stars and its latest release, v0.12.0, on 2026-10-10 ([open-webui/open-webui](https://github.com/open-webui/open-webui)); its licence keeps the Open WebUI branding in place above fifty end users ([LICENSE](https://raw.githubusercontent.com/open-webui/open-webui/main/LICENSE)). LibreChat ships MIT and 45,530 stars ([LibreChat-AI/LibreChat](https://github.com/LibreChat-AI/LibreChat)).

## Guides on the companion site

The companion site goes deeper on the decision behind this template:

- Start at the [Open WebUI alternative home page](https://openwebuialternative.com/) for what changes when a team moves from chat to agent work.
- Read the full [Kortix vs Open WebUI comparison](https://openwebuialternative.com/kortix-vs-open-web-ui) when you are weighing a chat UI against a platform.
- The [Open WebUI alternatives](https://openwebuialternative.com/best-open-webui-alternatives) page lists the other options teams consider.
- [Open WebUI for teams](https://openwebuialternative.com/open-webui-for-teams) covers what changes at team scale.
- The [self-hosting page](https://openwebuialternative.com/self-hosting) collects the deployment options side by side.

## Licence

Kortix is open source: read it, fork it, audit it. Each project you manage with Kortix is your own git repository, and you choose where it runs.
