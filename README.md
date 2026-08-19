# Discord Media Bot — Python Rewrite

A Python/Pycord rewrite of an earlier [Go Discord bot](https://github.com/ycallerisa/discord-media-bot-golang). The bot queries Scrolller's GraphQL API and returns selected images or videos through Discord slash commands, while restricting execution to channels marked as age-restricted.

The repository is a personal integration prototype and is not operated as a hosted public service.

> Content warning: this project retrieves adult media. Deploy it only in appropriately age-restricted Discord channels and in compliance with Discord's rules and applicable law.

## Goals of the rewrite

- replace text parsing with native Discord slash commands;
- reduce the amount of framework and data-model code;
- load the Discord token from the environment;
- use GraphQL variables for user-selected community names;
- keep channel validation in a shared guard;
- add explicit HTTP timeouts and centralized request handling.

## Commands

| Slash command | Behavior |
| --- | --- |
| `/pr0n` | Return random media from the discovery endpoint |
| `/pr0n_video subreddit:<name>` | Return the highest-width optimized video from a random post |
| `/pr0n_image subreddit:<name>` | Return the highest-width optimized image from a random post |

Every slash command calls `check_nsfw_and_increment` before making an external request. The current usage counter is held in memory per Discord server.

## Architecture

```text
Discord slash command
        |
        v
age-restricted channel guard
        |
        v
GraphQL request with variables
        |
        v
media filtering and selection
        |
        v
Discord response
```

| File | Responsibility |
| --- | --- |
| `main.py` | Bot startup, shared GraphQL client, commands and media selection |
| `random_sub_pic_or_vid.py` | Standalone discovery-query experiment |
| `sub_picture.py` | Standalone image-query experiment |
| `sub_video.py` | Standalone video-query experiment |

The three standalone scripts are development experiments; the running bot uses `main.py`.

## Local setup

Requirements:

- Python 3.10 or later;
- a Discord application and bot token;
- a Discord test server with an age-restricted channel.

Create a virtual environment and install the current runtime dependencies:

```bash
git clone https://github.com/ycallerisa/discord-media-bot-python.git
cd discord-media-bot-python

python -m venv .venv
source .venv/bin/activate
python -m pip install py-cord requests
```

Set the bot token without committing it:

```bash
export DISCORD_BOT_TOKEN="your-token"
python main.py
```

On Windows PowerShell:

```powershell
$env:DISCORD_BOT_TOKEN = "your-token"
python main.py
```

## Existing safety controls

- commands refuse to run outside channels marked as age-restricted;
- the Discord token is read from an environment variable;
- GraphQL operations pass the selected community through variables;
- external HTTP calls use a ten-second timeout;
- HTTP and JSON failures return a controlled Discord response instead of terminating the bot.

## Known limitations

- `requests` performs synchronous network calls inside asynchronous command handlers and can block the bot event loop;
- there is no per-user rate limit, concurrency limit or persistent quota;
- the community name is not yet length-bounded or validated against an allowlist;
- usage counters are lost on restart and are not safe across multiple instances;
- the external API is unofficial and may change without notice;
- the repository does not yet include a dependency manifest, automated tests or CI checks;
- the standalone scripts duplicate request and filtering logic from `main.py`.

A production-oriented revision should use an asynchronous HTTP client, add input validation and rate limiting, centralize the API adapter, persist operational state and test every authorization path.

## Engineering focus

This rewrite demonstrates migration between languages, third-party GraphQL integration, Discord interaction design and basic content-access controls. The comparison with the Go version documents the trade-off between a strongly typed, lower-level implementation and a smaller Python service optimized for iteration speed.

## Responsible use

Do not use the bot to bypass platform restrictions or distribute content without authorization. Server administrators remain responsible for access control, moderation and compliance.

