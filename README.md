# 🔍 sealed-room

<p align="center">
  <a href="https://github.com/calumjs/sealed-room/releases"><img src="https://img.shields.io/github/v/release/calumjs/sealed-room?label=release&color=8b0000" alt="Latest release"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-blue" alt="License: MIT"></a>
  <img src="https://img.shields.io/badge/Claude%20Code-plugin-d97757" alt="Claude Code plugin">
  <img src="https://img.shields.io/badge/Node.js-%E2%89%A5%2018-339933?logo=node.js&logoColor=white" alt="Node.js 18+">
</p>

**A murder-mystery game for [Claude Code](https://claude.com/claude-code) — refereed by an AI that genuinely cannot see who did it.**

A storm has sealed the roads. Someone in the house is a murderer. You question the suspects, search the rooms, and name your culprit. The twist: the assistant hosting the game **doesn't know the answer either.** It was generated, sealed to disk, and is only ever revealed through scripted output — so your AI detective-partner can react to the mystery alongside you without being able to spoil it. Every case is invented fresh — a new victim, a new cast, a new culprit.

## 🎬 Watch the showcase

https://github.com/user-attachments/assets/1316a7a8-8c41-4062-80b9-256bd6204b18

---

## How it stays unspoilable

This is the whole point, and it's just two ideas borrowed from Claude Code's own mechanics:

1. **Scripts run; only their *output* enters context.** When the case is dealt, `start-case` (in a forked context) invents the cast, fairly picks the murderer, and writes the solution to disk **encoded**. The main session only ever sees the **public briefing** — the victim, the room, the suspect line-up — never the murderer or any sheet.
2. **Interrogations run in a *forked* context.** When you question a suspect, a throwaway sub-context decodes *only that suspect's* secret sheet, plays them (lying if their sheet says to), and returns **only the spoken dialogue.** The killer's identity never crosses back into the main game.

No single interrogation contains "whodunit" anywhere — not even inside the fork. A suspect's sheet says *"deny you were in the library"*, never *"you are the murderer."* The answer only exists as the **sum of contradictions** you uncover. You win by out-reasoning everyone at the table, including the AI.

> **Honest caveat:** the sealing is **obfuscation, not security**. You are the operator of your own machine — you *can* dig the answer out of the encoded file if you really want to. The design guarantees no *accidental* spoilers and makes cheating take deliberate effort; it is not cryptographically secure against a determined player. Play in good faith. 🤝

---

## Install

Requires [Claude Code](https://claude.com/claude-code) and **Node.js** v18+ (used for the sealed generation and lookups). Don't have Node? `start-case` checks for it and will **offer to install it** for your OS before dealing the first case.

```bash
# add this repo as a plugin marketplace
/plugin marketplace add calumjs/sealed-room

# install the plugin
/plugin install sealed-room@sealed-room
```

## Play

```text
start a murder mystery        # deal a fresh case (cast, crime, sealed solution)
case file                     # re-read the public briefing & suspect list
interrogate Mrs Vale          # question a suspect — press them and watch for tells
examine the library           # search a room, the body, or an object for clues
accuse Mrs Vale               # name the killer — you only get to be sure once
```

A typical round:

1. **Deal it.** `start a murder mystery` → you get a briefing: the victim, where and when they died, and five suspects.
2. **Investigate.** `examine` the scene for physical clues; `interrogate` suspects about their whereabouts. Innocents have unrelated secrets (red herrings); exactly one person is lying about the crime itself.
3. **Catch the contradiction.** One honest witness can place the killer where they swore they never went. Find the alibi that can't survive it.
4. **Accuse.** `accuse <name>` unseals the truth and delivers the verdict.

---

## 🕯️ A sample case

```text
you ▸ start a murder mystery

      THE GREYWATER CONSERVATORY AFFAIR
      Edwin Marsh, a dealer in rare maps, is found dead in the
      conservatory at 9:40 PM, a storm raging outside.
      Five remain in the house — and one of them is lying.

you ▸ examine the conservatory

      An overturned orchid bench. A heavy brass watering can by the
      body — the weapon. An empty hook by the door, labelled GLOVES.
      The gloves are gone.

you ▸ interrogate Felix Crane     (the groundskeeper)

      "I was down at the boathouse from half past nine. I'd no
       cause to go near the conservatory."

you ▸ interrogate Mrs Tilney      (the housekeeper)

      "Ask young Crane how a gardener affords new boots all of a
       sudden — and what became of the gloves on that hook."

you ▸ accuse Felix Crane

      ✅ CORRECT — the murderer was Felix Crane.
         The AI host never knew, until you said it.
```

> **In one move:** ask your assistant to *"use a workflow to interrogate every suspect at once"* — all five questioned **in parallel**, each in its own forked context, blind to the others. (That's the screen the showcase opens on.)

---

## The skills

| Skill | What it does |
|---|---|
| `start-case` | Invents the cast & crime in a **forked context**, fairly coin-flips the murderer, seals the solution to disk, then presents only the **public briefing** — never the murderer or a sheet. (Also checks for Node.js and offers to install it.) |
| `interrogate` | Forks, decodes **only the named suspect's** sheet, role-plays them (lying per their sheet), and returns only dialogue. |
| `examine` | Surfaces the clue for a room / the body / an object. The solution stays inside the script. |
| `accuse` | Unseals the solution, checks your guess, and reveals the full truth. |
| `case-file` | Re-shows the public briefing. Never touches the sealed files. |

All five are **fully inline** — the logic lives in the `SKILL.md` files as `!`node -e …`` injections, with no bundled scripts. Game state is written to a `.murder-case/` folder in your current working directory (safe to delete; add it to `.gitignore`).

---

## How it works (for the curious)

```
start-case  ──(forked)──►  invent cast + fair coin-flip for culprit
                           write each suspect a sheet (truths AND lies)
                           seal everything to .murder-case/sealed/case.dat (encoded)
                           ► presents only the PUBLIC briefing  ◄ murderer never leaves the fork

interrogate ──(forked)──►  decode ONE suspect's sheet  ►  role-play  ►  returns ONLY dialogue
examine     ───────────►   decode + print ONE clue     ►  solution never leaves the process
accuse      ───────────►   decode + compare your guess ►  unseal & reveal (endgame)
```

The "sealed room" is both the genre *and* the architecture: the answer is locked away somewhere even the host can't open mid-game.

---

## License

MIT © Calum Simpson. See [LICENSE](LICENSE).

---

*Built with Claude Code — and itself a small demo of the mechanics it's built on: progressive disclosure, forked contexts, and the script-output boundary, turned into a game you can't be spoiled by.*
