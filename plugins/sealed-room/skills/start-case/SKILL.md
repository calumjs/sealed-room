---
name: start-case
description: Start a new murder mystery game — invent a fresh cast and crime, fairly pick the murderer, and seal the solution so it stays hidden. Use when the user says "start a murder mystery", "new case", "deal a mystery", "let's play detective", "begin the whodunit", or runs /start-case.
allowed-tools: Bash(node:*), Write, Read
context: fork
---

# Start a Murder Mystery (authored live, in a forked context)

You are running in a FORKED context. You will INVENT a fresh mystery, seal the solution to disk, then **set the scene** by presenting the public briefing. The main game session must never learn the murderer — only the public briefing ever leaves this fork.

## 0. Make sure Node.js is available (do this FIRST)
sealed-room runs on Node.js. Before generating anything, check it:

```
node --version
```

- If it prints a version (v18+ ideally), continue to step 1.
- If `node` is **not found**, do NOT generate a case. Tell the player Node.js is required and **offer to install it**, then install it — *only with their go-ahead* — using the command that fits their machine:
  - **Windows (winget):** `winget install -e --id OpenJS.NodeJS.LTS`
  - **macOS (Homebrew):** `brew install node`
  - **Debian / Ubuntu:** `sudo apt-get update && sudo apt-get install -y nodejs`
  - **Fedora / RHEL:** `sudo dnf install -y nodejs`
  - **No package manager / unsure:** point them to <https://nodejs.org> (download the LTS installer).
- After installing, `node` may not be on `PATH` until they open a new terminal or restart Claude Code. Ask them to do that, then run **start a murder mystery** again.

Do not proceed past this step until `node --version` succeeds.

## 1. Fair coin-flip — who is guilty
The guilty suspect's number, chosen at random and fairly:  !`node -e "console.log(require('crypto').randomInt(5)+1)"`

Hold on to that number. The suspect you place at that position in step 2 is the murderer.

## 2. Invent the case
- A **victim**: name, the room they died in, the time, and an everyday **weapon-of-opportunity**.
- Exactly **5 suspects**, each with a distinct name, role, and voice. The suspect at the coin-flip number above is the murderer.
- Make it **solvable** (critical):
  - Exactly ONE other suspect is a **witness** whose truthful account places the murderer near the scene at the time.
  - The murderer has a **false alibi** that the witness's account contradicts.
  - The remaining suspects each hide an **unrelated guilty secret** (red herrings) — shifty, but innocent.
- Give each suspect a **sheet**: identity, demeanour, stated alibi, what they know, what they must conceal/deny, their tell, and how they behave under pressure. **Never** write "you are the murderer" in a sheet — encode guilt only as what they must deny and how they crack.
- A few **clues** for `examine`: the murder room (struggle + the weapon on the floor), `body` (room/time/weapon), and 1–2 other rooms ("nothing here").

## 3. Seal it (inline; leaves no plaintext solution behind)
a. Use the Write tool to save the full case as plaintext JSON at `.murder-case/_tmp.json`, exactly this shape:
   ```json
   { "victim": "", "location": "", "time": "", "weapon": "", "murderer": "",
     "suspects": [ { "name": "", "role": "" } ],
     "sheets": { "<name>": { "identity": "", "demeanor": "", "alibi_claim": "", "knows": [], "conceal": [], "tell": "", "pressure": "" } },
     "clues": { "<place>": "", "body": "" },
     "briefing": "<PUBLIC markdown: victim + room + time; the 5 suspects, each with their name, role, AND their stated alibi (the one-line `alibi_claim` — where they say they were when the victim died); and a short how-to-play. The stated alibis are public and MUST appear here so the player starts with everyone's story on the record. NEVER reveal the murderer, the weapon-as-proof, who the witness is, or any hidden sheet detail (what a suspect knows, conceals, their tell, or how they crack).>" }
   ```
b. Run this inline sealer (it encodes the case, writes the public briefing, and deletes the plaintext):
   ```
   node -e "const fs=require('fs'),K=Buffer.from('a-mystery-most-foul');const j=JSON.parse(fs.readFileSync('.murder-case/_tmp.json','utf8'));const s=Buffer.from(JSON.stringify(j),'utf8'),o=Buffer.alloc(s.length);for(let i=0;i<s.length;i++)o[i]=s[i]^K[i%K.length];fs.mkdirSync('.murder-case/sealed',{recursive:true});fs.writeFileSync('.murder-case/sealed/case.dat',o.toString('base64'));fs.writeFileSync('.murder-case/briefing.md',j.briefing);fs.unlinkSync('.murder-case/_tmp.json');fs.writeFileSync('.murder-case/actions','0');console.log('SEALED '+j.suspects.length+' suspects')"
   ```

## 4. Set the scene (the opening briefing)
The solution is sealed — now open the game for the player.

Returning detective's record:  !`node -e "const fs=require('fs');const f='.murder-case/highscores.json';let arr=[];try{arr=JSON.parse(fs.readFileSync(f,'utf8'))}catch(e){}if(!arr.length){console.log('NONE');process.exit(0)}const s=arr.filter(r=>r.result==='solved');const best=s.length?Math.min.apply(null,s.map(r=>r.score)):null;console.log('record :: '+arr.length+' game(s), '+s.length+' solved'+(best!=null?', best score '+best:'')+' :: last case: '+arr[arr.length-1].mystery)"`

- If that line is anything other than `NONE`, **open by welcoming the returning detective with their record** — games played, how many solved, best score, and their last case — a quick "previously…" before the new mystery. Mention they can run `scores` for the full leaderboard.
- Then present the **public briefing**: read `.murder-case/briefing.md` (PUBLIC — it contains no solution) and set the scene in an atmospheric host voice — the victim, where and when they died, and the suspect line-up. For each suspect, give their name, role, **and their stated alibi** — every suspect's story belongs on the record from the outset, so the player can start playing the alibis against each other (and against `examine` findings) instead of having to interrogate everyone just to learn where they each *claim* to have been.
- Close with what they can do next: **interrogate** a suspect, **examine** a place or the body, or **accuse** when they are ready.

Reveal **nothing** beyond the public briefing — never the murderer, the weapon-as-proof, who the witness is, or any hidden sheet detail (what a suspect *knows*, what they conceal, their tell, or how they crack under pressure). The five **stated alibis** are public and belong in the briefing; the truth that breaks one of them is not. (The high-scores file is public and safe to read; the sealed case is not.)
