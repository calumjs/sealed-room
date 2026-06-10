---
name: case-file
description: Review the public briefing and suspect list for the current murder mystery. Use when the user says "case file", "show the briefing", "who are the suspects", "recap the case", "remind me of the suspects", or runs /case-file.
allowed-tools: Read
---

# Case File

Show the player the public case briefing by reading `.murder-case/briefing.md`, then remind them of their options: `interrogate <suspect>`, `examine <place>`, `accuse <suspect>`.

Never read anything under `.murder-case/sealed/` — that is where the hidden solution lives.
