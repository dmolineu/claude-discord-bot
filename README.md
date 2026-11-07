# claude-discord-bot

A personal Discord bot that surfaces the author's own Gmail and Google Calendar
in a private Discord server. Single-user; not offered to the public.

Google API scopes used, both read-only:
`gmail.readonly`, `calendar.readonly`

See [PRIVACY.md](PRIVACY.md).

## Token rotation

Rotate the Discord bot token about every 90 days. Once the token is older than
that, the bot posts a weekly 🔑 reminder until `TOKEN_CREATED_AT` is updated.

The bot reads its config from `/etc/cloud-ai-assistant/discord.env` (root-owned,
mode 600) and runs as the systemd unit `claude-discord-bot.service`.

1. In the [Discord Developer Portal](https://discord.com/developers/applications),
   open the app, go to the **Bot** tab, click **Reset Token** and copy the new
   token. Discord shows it only once, and the old token stops working
   immediately, so do the remaining steps right away.
2. On the host, edit the env file:

   ```sh
   sudo nano /etc/cloud-ai-assistant/discord.env
   ```

   Replace the `DISCORD_TOKEN=` line with the new token and set
   `TOKEN_CREATED_AT` to today's date in ISO 8601 form (for example
   `TOKEN_CREATED_AT=2026-11-07`; a full timestamp also works, since the bot
   parses it with `new Date()`). Save the file.
3. Restart the bot so it picks up the new values:

   ```sh
   sudo systemctl restart claude-discord-bot
   ```

4. Verify:

   ```sh
   sudo systemctl status claude-discord-bot --no-pager
   ```

   Then DM the bot `!status` and confirm it reports `Token age: 0 days`.

The weekly reminder stops as soon as `TOKEN_CREATED_AT` is a date within the
last 90 days.
