# Discord Media Bot (Python)

This project is a Python rewrite of a Discord bot that fetches and posts random media from Scrolller and Reddit subreddits using the GraphQL API.
It exposes several slash commands to retrieve images and videos and is restricted to NSFW Discord channels.

Important: This bot is designed for NSFW media and must only be used in Discord channels that are explicitly marked as NSFW. Use responsibly and respect Discord’s Terms of Service.

## Features

- Discord slash commands for fetching media  
  - /pr0n – fetch random media from NSFW subreddits discovered via Scrolller  
  - /pr0n_video – fetch a high-quality optimized video from a specific subreddit  
  - /pr0n_image – fetch a high-quality optimized image from a specific subreddit  
- GraphQL integration with Scrolller’s API (DiscoverSubreddits and Subreddit queries)  
- Channel-level NSFW checks to ensure commands are only used in NSFW channels  
- Per-guild usage counter with a donation message every 100 successful calls  
- Simple, self-contained Python codebase that is easy to extend with new commands or filters

## Tech Stack

- Language: Python 3  
- Discord framework: discord.py (discord.ext.commands with slash commands)  
- HTTP client: requests  
- API: Scrolller GraphQL API (custom queries and variables)

## Getting Started

### Prerequisites

- Python 3.10 or newer  
- A Discord application and bot token  
- A Discord server where you can add and test the bot  
- pip to install Python dependencies

### Installation

1. Clone the repository

    git clone https://github.com/kathiouchka/discord-media-bot-python.git
    cd discord-media-bot-python

2. (Optional) Create and activate a virtual environment

    python -m venv .venv  
    source .venv/bin/activate  
    (On Windows: .venv\Scripts\activate)

3. Install dependencies

    pip install -r requirements.txt

A typical requirements.txt for this project would include:
discord.py  
requests

4. Set your Discord bot token as an environment variable

    export DISCORD_BOT_TOKEN="your_bot_token_here"

On Windows PowerShell:
    $env:DISCORD_BOT_TOKEN="your_bot_token_here"

## Running the Bot

Run the bot with:

    python main.py

When the bot is connected, you should see a log message in your terminal.
Make sure the bot has the required permissions and the applications.commands scope when you invite it to your server.

## Usage

All commands must be executed in a Discord channel marked as NSFW.

### /pr0n  
Fetch a random piece of NSFW media from discovered NSFW subreddits via Scrolller.

### /pr0n_video subreddit:<name>  
Fetch an optimized video from a specific subreddit (best width and optimized source).

### /pr0n_image subreddit:<name>  
Fetch an optimized image from a specific subreddit (best width and optimized source).

If a command is used in a non-NSFW channel, the bot sends a warning and refuses to post media.

Every 100 successful requests per guild, the bot sends a donation reminder message.

## Internals

The bot is structured around:

- A shared make_api_request function that performs HTTP POST requests against the Scrolller GraphQL endpoint with timeout and error handling  
- Two main GraphQL queries:  
  - DiscoverSubredditsQuery for discovering NSFW subreddits and retrieving child media items  
  - SubredditQuery for retrieving posts from a specific subreddit with VIDEO or PICTURE filters  
- A helper named check_nsfw_and_increment that:  
  - Ensures the command is executed in a NSFW channel  
  - Tracks per-guild usage and triggers a donation reminder periodically

Media selection logic prioritizes:

- Optimized sources (isOptimized = true)  
- Highest available width for the best quality  
- Video sources ending in mp4 or webm

## Possible Improvements

- Add a configuration file or environment variables to:  
  - Whitelist or blacklist specific subreddits  
  - Adjust media filters (minimum width, allowed hosts, etc.)  
- Add unit tests for:  
  - Media selection logic  
  - NSFW gating behavior  
- Add a small CI workflow (GitHub Actions) for linting and tests  
- Refactor commands into separate modules for maintainability  
- Provide a "safe mode" using SFW subreddits for demos

## Disclaimer

This bot interacts with NSFW content and must only be used in compliance with Discord’s Terms of Service and local regulations.
The author is not responsible for misuse or policy violations related to this bot.
