# Secrets — The Alphabet Cartel

This directory holds Docker Secret files for TAC's bots. These files are **never committed to git**.

## Required Secrets

| File | Description | Source |
|------|-------------|--------|
| `portia_fluxer_token` | Fluxer bot token for Portia | Fluxer Developer Portal |
| `prism_fluxer_token` | Fluxer bot token for Prism | Fluxer Developer Portal |
| `puck_fluxer_token` | Fluxer bot token for Puck | Fluxer Developer Portal |
| `twitch_client_id` | Twitch application Client ID | [Twitch Developer Console](https://dev.twitch.tv/console/apps) |
| `twitch_client_secret` | Twitch application Client Secret | [Twitch Developer Console](https://dev.twitch.tv/console/apps) |
| `youtube_api_key` | Google/YouTube Data API v3 key | [Google Cloud Console](https://console.cloud.google.com/apis/credentials) |

---

## Setup: Fluxer Bot Tokens

1. Obtain a bot token from the Fluxer Developer Portal for each bot
2. Create the secret files:
   ```bash
   printf "YOUR_PORTIA_TOKEN_HERE" > ./secrets/portia_fluxer_token
   printf "YOUR_PRISM_TOKEN_HERE" > ./secrets/prism_fluxer_token
   printf "YOUR_PUCK_TOKEN_HERE" > ./secrets/puck_fluxer_token
   ```

---

## Setup: Twitch API Credentials

Puck uses the Twitch Helix API with **client credentials** (app access token) to check
stream status. This requires a Twitch application with a Client ID and Client Secret.

### Prerequisites

- A Twitch account (any account works — does not need to be a streamer)
- Two-factor authentication (2FA) enabled on the Twitch account (required by Twitch
  to access the Developer Console)

### Step-by-Step

1. **Enable 2FA on your Twitch account** (if not already done)
   - Go to [twitch.tv/settings/security](https://www.twitch.tv/settings/security)
   - Under **Two-Factor Authentication**, click **Set Up Two-Factor Authentication**

2. **Open the Twitch Developer Console**
   - Go to [dev.twitch.tv/console](https://dev.twitch.tv/console)
   - Log in and authorize access

3. **Register a new application**
   - Click **Applications** → **Register Your Application**
   - **Name**: `Puck - Alphabet Cartel` (must be unique across Twitch)
   - **OAuth Redirect URLs**: `http://localhost:3000` (required but unused)
   - **Category**: `Application Integration`

4. **Get your Client ID and Secret**
   - Click **Manage** next to your application
   - Copy the **Client ID**
   - Click **New Secret**, copy immediately (shown only once)
   - Create the secret files:
     ```bash
     printf "YOUR_CLIENT_ID_HERE" > ./secrets/twitch_client_id
     printf "YOUR_CLIENT_SECRET_HERE" > ./secrets/twitch_client_secret
     ```

### Important Notes

- The Client Secret is shown **only once**. If lost, generate a new one (invalidates the old).
- Puck uses client credentials (server-to-server) — no user auth or scopes required.
- Do **not** share Client IDs between applications.

---

## Setup: YouTube Data API v3 Key

Puck uses the YouTube Data API v3 to check if tracked channels are live streaming.

### Step-by-Step

1. **Open the Google Cloud Console**
   - Go to [console.cloud.google.com](https://console.cloud.google.com)

2. **Create a new project**
   - Name it something like `Puck Stream Monitor` or `Alphabet Cartel Bots`

3. **Enable the YouTube Data API v3**
   - Go to **APIs & Services** → **Library** → search `YouTube Data API v3` → **Enable**

4. **Create an API Key**
   - Go to **APIs & Services** → **Credentials** → **Create Credentials** → **API Key**
   - Copy the key and create the secret file:
     ```bash
     printf "YOUR_API_KEY_HERE" > ./secrets/youtube_api_key
     ```

5. **Restrict the API key** (recommended)
   - Edit the key → **API restrictions** → restrict to **YouTube Data API v3** only

### Important Notes

- Default quota: **10,000 units/day**. Each `search.list` call costs 100 units.
- Puck uses RSS pre-checks to conserve quota before making API calls.
- Quota resets daily at midnight Pacific Time.
- Do **not** restrict by IP unless Bragi's outbound IP is static and known.

---

## Deploying Secrets

After creating all secret files, set permissions:

```bash
chmod 600 ./secrets/portia_fluxer_token
chmod 600 ./secrets/prism_fluxer_token
chmod 600 ./secrets/puck_fluxer_token
chmod 600 ./secrets/twitch_client_id
chmod 600 ./secrets/twitch_client_secret
chmod 600 ./secrets/youtube_api_key
```

## Security Reminders

- Secret files are referenced by `docker-compose.yml` and mounted at `/run/secrets/`
- Bots read them via the `load_secret()` pattern in `config_manager.py`
- Never commit these files — only this README is tracked
- Rotate secrets periodically and after any suspected exposure
- The `secrets/` directory is gitignored in this repository

---

**Built with care for chosen family** 🏳️‍🌈
