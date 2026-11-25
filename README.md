# Prun AI

Give AI the power to answer any Prun question with disconcering confidence! Caveat emptor...

## Sources

This project provides LLMs with various sources of information and live data:

- An unofficial [FIO CLI](https://github.com/lumivient/fio-cli) allows live querying of game data
- `docs/GAME.md` provides an overview of game mechanics (adapted from https://github.com/refined-prun/refined-prun/)
- `docs/TERMINOLOGY.md` defines common terms LLMs often speculate and are wrong about
- [The Official Handbook](https://handbook.apex.prosperousuniverse.com/index.html) documents various aspects of the game
- [Community Derived Information](https://pct.fnar.net/) fleshes out some lesser-known details

## Dependencies

- [Python 3.11+](https://www.python.org/)
- [uv](https://docs.astral.sh/uv/)

## Setup

1. `git clone https://github.com/lumivient/prun-ai.git`
2. `cd prun-ai`
3. `mkdir -p ~/.config/prun-ai && cp config.toml.example ~/.config/prun-ai/config.toml`
4. Get an FIO API key from https://fio.fnar.net/ if you haven't already
5. Edit `~/.config/prun-ai/config.toml` and enter your details

## Usage

Just start an AI chat in this folder and ask away...

Either create a Project in Claude/ChatGPT/etc. with this as the working folder

Or `cd` here and run `claude`/`codex`/etc.

## Updates

If it misbehaves or gives outdated information, it may need updating — run `./update`
