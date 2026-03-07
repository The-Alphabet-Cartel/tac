<p align="center">
  <img src="images/TAC-Logo.png" alt="The Alphabet Cartel Logo" width="200" />
</p>

# The Alphabet Cartel — Fluxer Bots

Bot deployment stack for [The Alphabet Cartel](https://fluxer.gg/yGJfJH5C), an LGBTQIA+ gaming, mental health, and advocacy community on Fluxer.

This repo is a submodule of [Bragi](https://github.com/the-alphabet-cartel/bragi) — the top-level orchestration hub. It manages TAC's bot stack and references each bot as its own Git submodule.

---

## The Bot Family

| Bot | Role | Description |
|-----|------|-------------|
| [**Portia**](https://github.com/the-alphabet-cartel/portia) | Voice Channel Manager | Creates private temp voice channels when users join the lobby. Cleans up automatically when empty. Named for Shakespeare's advocate in *The Merchant of Venice*. |
| [**Prism**](https://github.com/the-alphabet-cartel/prism) | Member Onboarding | Assigns the base member role when someone posts in `#introductions`. Named for how light through a prism reveals every color. |
| [**Puck**](https://github.com/the-alphabet-cartel/puck) | Stream Monitor | Watches Twitch and YouTube for community members going live, toggling a "Live" role for visibility. Named for Shakespeare's mischievous herald in *A Midsummer Night's Dream*. |

All bots follow the architectural patterns defined in the [Bragi Clean Architecture Charter](https://github.com/the-alphabet-cartel/bragi/blob/main/docs/standards/charter.md).

---

## Repository Structure

```
tac/
├── portia/                   ← Git submodule → the-alphabet-cartel/portia
├── prism/                    ← Git submodule → the-alphabet-cartel/prism
├── puck/                     ← Git submodule → the-alphabet-cartel/puck
├── docker-compose.yml        ← TAC bot stack
├── .env.template             ← Environment variable reference
└── secrets/
    └── README.md             ← Credential setup guide (committed)
```

---

## Deployment

### Prerequisites

- **Server:** Debian Linux with Docker Engine 29.x + Compose v5
- Fluxer bot tokens for each bot (see [`secrets/README.md`](secrets/README.md))
- Twitch and YouTube API credentials for Puck (see [`secrets/README.md`](secrets/README.md))

### Setup

```bash
# 1. Clone with submodules (from within the bragi repo)
git submodule update --init --recursive

# 2. Create the Docker network
docker network create tac-net

# 3. Create host directories for persistent data
mkdir -p /opt/bragi/tac/portia/{logs,data}
mkdir -p /opt/bragi/tac/prism/{logs,data}
mkdir -p /opt/bragi/tac/puck/{logs,data}

# 4. Configure environment
cp .env.template .env
# Edit .env — set GUILD_ID and bot-specific channel/role IDs

# 5. Create secrets (see secrets/README.md for detailed instructions)

# 6. Deploy the TAC bot stack
docker compose up -d
```

### Managing the Stack

```bash
# View all bot logs
docker compose logs -f

# Restart a single bot
docker compose restart portia

# Pull latest images and redeploy
docker compose pull && docker compose up -d
```

---

## Platform

TAC's bots run on [Fluxer](https://fluxer.gg), a Discord-alternative platform, using the [fluxer-py](https://github.com/akarealemil/fluxer.py) library.

---

**Built with care for chosen family** 🏳️‍🌈
