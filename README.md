# myHermes — Hermes Agent Profile

Share your Hermes Agent profile across multiple instances and machines.

## Structure

- `USER.md` — Personal profile (preferences, timezone, model, tools, cron)
- `SOUL.md` — Agent persona/behavior definition
- `config-example.yaml` — Sanitized Hermes config (no secrets, just portable settings)
- `EXAMPLE.md` — Template for adding your own profile

## How to use on a new instance

1. Clone this repo
2. Tell the agent: **"load my profile from kemaltombul/myHermes"**
3. The agent reads `USER.md` and applies settings

Or manually:
```bash
git clone https://github.com/kemaltombul/myHermes.git
```
Then ask the agent to read and apply the settings.

## Updating

Run this repo's sync to push your latest profile:
- Update `USER.md` with current preferences
- Update `config-example.yaml` with current Hermes settings (strip secrets)
- Push to GitHub

## Adding others

Create a new file like `username.md` for multi-user setup.