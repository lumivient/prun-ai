# Instructions for AIs

## Project Summary

This project gives YOU (the Assistant) access to documentation and live data so you can help the User with queries about Prosperous Universe, the MMO management and economy game. Thus you can research and answer complicated questions from a highly informed position, without speculation.

## Process Steps

Goal: answer user question with real data and clear reasoning.

Required flow:

1. Restate question in one line.
2. If the questions requires Run `uv run fio context` to ensure you have the right basic info.
3. Read `GAME.md` if mechanics interpretation is needed.
4. Run `uv run fio shape <endpoint>` before any payload fetch so you know exact structure, endpoint metadata, and valid field names for filtering.
5. Always fetch data with `--filter` using JMESPath (usage: `uv run fio get <endpoint> --filter '<JMESPath>'`) unless full payload is absolutely necessary — state justification before doing so.
6. Never use `--pretty` (unless the user explicitly instructs you to)
7. Identify required data and run `uv run fio` commands to fetch it.
8. Present key intermediate data points/tables/calculations.
9. Give direct conclusion, assumptions, and uncertainty.
10. End with a concise summary of the conclusion.

Rules:
- Do not invent mechanics or data.
- If docs and data conflict, stop and state conflict explicitly.
- Include exact commands used so user can reproduce.
- If required data unavailable, say exactly what is missing.

NEVER blindly assume how the game works. If you don't 100% know how a feature or UI component works, consule the data sources above. If there is no relevant info in these sources, make an assumption and ask the user if it is correct before proceeding. Only then you are allowed to follow an assumption.

## Context Optimisation

Before running any data-fetch command that can return API payloads, agents MUST do this flow:

1. Discover shape first
- Run `uv run fio shape <endpoint>`.
- Identify minimal fields/rows needed for the task.

2. Filter before output
- Run `uv run fio get <endpoint> --filter '<JMESPath expression>'`.
- Treat `--filter` as mandatory by default. Use the narrowest possible filter.
- Always constrain to exact business keys when available. Example for prices: filter by both `Ticker` and `CX`, not just one.
- Pull only the rows/fields needed for the answer so output stays token-efficient and easier to verify.

3. Pretty only for humans
- Keep default compact output for AI context efficiency.
- Use `--pretty` only when user explicitly asks.

4. No unfiltered full payloads by default
- Do not run raw `uv run fio get <endpoint>` without `--filter` unless:
  - user explicitly asks for full output, or
  - shape/filter is impossible for task.
- If unfiltered output is unavoidable, state why in one line before command.
