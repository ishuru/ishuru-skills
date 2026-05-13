---
name: 'Google CLI Token Ops'
description: "Use when running Google CLI commands (gcloud, gsutil, gmr, gam, google-drive-cli) for a non-default Google account, scripting multi-account Google operations with different tokens, when a tool authenticates as the wrong Google account, or when automating Gmail/Drive/Calendar across personal, work, and Astemo identities. Use when user says 'gog-cli', 'google account switch', 'wrong google account', 'gcloud as', or needs to script Google API access for multiple accounts."
icon: icon.svg
metadata:
  depends_on: [astemo,github-cli-token-ops]
  category: system/security
  family: system
  lifecycle: active
  canonical_slug: gog-cli
  priority: 80
  icon_style: craft-category-glyph-v1
---

# Google CLI Multi-Account Token Operations

## Overview

Google CLI tools (`gcloud`, `gsutil`, GAM, etc.) use `~/.config/gcloud/` or similar for auth state. When automating with a different account, **override per-command** with environment variables — no account switching needed, no config file modifications.

Pattern mirrors `github-cli-token-ops`: token-per-command override, zero config mutation.

## Known Accounts

| Label | Email | Use |
|-------|-------|-----|
| personal | `sabarish.duvvuru@gmail.com` | Primary personal (Recorder, personal Drive, YouTube) |
| work | `dsabarish.work@gmail.com` | Chrome work profile, secondary personal |
| ishuru | `ishuru.dev@gmail.com` | Dev/experiments |
| astemo | `sabarish.duvvuru.qx@astemo.com` | Enterprise (M365-adjacent, but Google services) |

## Per-Command Token Override

### gcloud

```bash
# Override for a single command
CLOUDSDK_ACTIVE_CONFIG=personal gcloud projects list
CLOUDSDK_ACTIVE_CONFIG=work gcloud auth login

# Named configurations (persistent, but non-destructive)
gcloud config configurations create personal
gcloud config configurations activate personal
gcloud auth login
```

### Google OAuth tokens (GAM, custom scripts)

```bash
# Per-command token override via env var
GOOGLE_TOKEN=$(cat ~/.config/gog-cli/personal.token) gam <command>
GOOGLE_TOKEN=$(cat ~/.config/gog-cli/astemo.token) gam <command>
```

### Google Drive CLI / rclone

```bash
# rclone with named remote
rclone lsf personal:/
rclone lsf work:/
rclone copy personal:/path ./local --progress
```

## Token Storage Convention

Store tokens under `~/.config/gog-cli/`:

```
~/.config/gog-cli/
  personal.token    # sabarish.duvvuru@gmail.com
  work.token        # dsabarish.work@gmail.com
  ishuru.token      # ishuru.dev@gmail.com
  astemo.token      # sabarish.duvvuru.qx@astemo.com
  accounts.json     # Machine-readable account inventory
```

### accounts.json schema

```json
{
  "accounts": {
    "personal": {
      "email": "sabarish.duvvuru@gmail.com",
      "label": "Primary personal",
      "token_file": "~/.config/gog-cli/personal.token",
      "services": ["drive", "gmail", "calendar", "youtube", "recorder"]
    },
    "work": {
      "email": "dsabarish.work@gmail.com",
      "label": "Chrome work profile",
      "token_file": "~/.config/gog-cli/work.token",
      "services": ["drive"]
    },
    "ishuru": {
      "email": "ishuru.dev@gmail.com",
      "label": "Dev experiments",
      "token_file": "~/.config/gog-cli/ishuru.token",
      "services": ["drive", "cloud"]
    },
    "astemo": {
      "email": "sabarish.duvvuru.qx@astemo.com",
      "label": "Astemo enterprise",
      "token_file": "~/.config/gog-cli/astemo.token",
      "services": ["drive", "calendar", "gmail"]
    }
  },
  "default": "personal"
}
```

## Check Which Account Is Active

```bash
# gcloud
gcloud config configurations list --format="value(name,account)"
gcloud auth list --format="value(account)"

# Custom token (check email from token)
curl -s -H "Authorization: Bearer $GOOGLE_TOKEN" \
  https://www.googleapis.com/oauth2/v2/userinfo | jq '.email'
```

## Common Automation Tasks

### Gmail (via gam or google-cli)

```bash
# Read recent inbox with specific account
GOOGLE_ACCOUNT=personal gam inbox --max 10

# Send email from specific account
GOOGLE_ACCOUNT=personal gam send recipient@example.com "Subject" "Body"
```

### Google Drive Operations

```bash
# List files from specific account
rclone lsf personal:/ --max-depth 1
rclone lsf astemo:/ --max-depth 1

# Download file from specific account
rclone copy astemo:/Shared/project.xlsx ./tmp/
```

### gcloud (GCP)

```bash
# List projects for specific config
CLOUDSDK_ACTIVE_CONFIG=personal gcloud projects list --format="table(name,projectId,state)"

# Deploy function to specific account's project
CLOUDSDK_ACTIVE_CONFIG=personal gcloud functions deploy myfunc --trigger-http
```

## Service-Specific Notes

### Gmail / Google API

- OAuth scopes: `mail.google.com` (full), `https://www.googleapis.com/auth/gmail.readonly` (read)
- Token type: OAuth 2.0 access token (expires) or refresh token (persistent)
- Use Google Cloud Console → Credentials → OAuth 2.0 Client IDs to generate tokens
- For agent automation: use service account key or user refresh token stored in `~/.config/gog-cli/`

### Google Drive

- rclone config: set up named remotes (`personal`, `work`, `astemo`) in `~/.config/rclone/rclone.conf`
- Each remote has its own OAuth token, isolated by remote name
- `rclone lsf <remote>:/path` — no cross-account leaking

### gcloud

- Named configurations: `gcloud config configurations create|list|activate|delete`
- Each config stores its own project, region, and credentials
- `CLOUDSDK_ACTIVE_CONFIG` env var overrides the active configuration per-command

## Cross-Account Copy

```bash
# Copy file between Google accounts via local staging
rclone copy personal:/path/to/file ./tmp/staging/
rclone copy ./tmp/staging/ astemo:/destination/
rm -rf ./tmp/staging/

# Direct server-side copy (same account only)
rclone copy personal:/src/ personal:/dest/ --drive-server-side-across-configs
```

## Token Refresh

OAuth tokens expire. Store refresh tokens and refresh before use:

```bash
# Refresh a token (requires client_id and client_secret)
CLIENT_ID="your-client-id.apps.googleusercontent.com"
CLIENT_SECRET="your-client-secret"
REFRESH_TOKEN=$(cat ~/.config/gog-cli/personal.refresh)

ACCESS_TOKEN=$(curl -s -X POST \
  "https://oauth2.googleapis.com/token" \
  -d "client_id=$CLIENT_ID" \
  -d "client_secret=$CLIENT_SECRET" \
  -d "refresh_token=$REFRESH_TOKEN" \
  -d "grant_type=refresh_token" | jq -r '.access_token')

export GOOGLE_TOKEN="$ACCESS_TOKEN"
```

## Security Notes

- **Never commit tokens** to git. Add `~/.config/gog-cli/*.token` to `.gitignore`.
- **Service accounts** for automation: store JSON keys in `~/.config/gog-cli/keys/`, not in project dirs.
- **Token rotation**: refresh tokens can be revoked. Rotate annually or after suspected exposure.
- **Principle of least access**: use readonly scopes when possible; use service accounts with minimal IAM roles.

## Relationship to Other Skills

- **github-cli-token-ops**: Same per-command override pattern, adapted for Google.
- **reverse-github**: Multi-account inventory pattern, adapted for Google accounts.
- **craft-google-oauth**: Craft's built-in Google OAuth trigger for session-bound access (single account).
- **astemo-dropbox**: Dropbox-first routing for Astemo finals; use `gog-cli` only when Google Drive is explicitly needed.

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Wrong Google account responds | Use `CLOUDSDK_ACTIVE_CONFIG=` or named rclone remote; don't rely on active login |
| Token expired mid-script | Use refresh tokens; refresh at script start, not on first use |
| rclone uses wrong remote | Check `rclone listremotes`; use explicit remote name prefix |
| gcloud project mismatch | `CLOUDSDK_ACTIVE_CONFIG` sets both account and project; verify with `gcloud config get-value project` |
| Accidental cross-account write | Always verify active account before destructive operations |
| Token in repo / logs | Store tokens in `~/.config/gog-cli/`, not in project dirs or env files |
