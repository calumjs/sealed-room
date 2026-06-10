---
name: start-case
description: Start a new murder mystery game — invent a fresh cast and crime, fairly pick the murderer, and seal the solution so it stays hidden. Use when the user says "start a murder mystery", "new case", "deal a mystery", "let's play detective", "begin the whodunit", or runs /start-case.
allowed-tools: Bash(node:*), Write, Read
context: fork
---

# Start a Murder Mystery (authored live, in a forked context)

You are running in a FORKED context. You will INVENT a fresh mystery, seal the solution to disk, and return ONLY a spoiler-free line. The main game session must never learn the murderer.

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
     "briefing": "<PUBLIC markdown: victim + room + time, the 5 suspect names & roles, and a short how-to-play. NEVER reveal the murderer, the weapon-as-proof, or any sheet.>" }
   ```
b. Run this inline sealer (it encodes the case, writes the public briefing, and deletes the plaintext):
   ```
   node -e "const fs=require('fs'),K=Buffer.from('a-mystery-most-foul');const j=JSON.parse(fs.readFileSync('.murder-case/_tmp.json','utf8'));const s=Buffer.from(JSON.stringify(j),'utf8'),o=Buffer.alloc(s.length);for(let i=0;i<s.length;i++)o[i]=s[i]^K[i%K.length];fs.mkdirSync('.murder-case/sealed',{recursive:true});fs.writeFileSync('.murder-case/sealed/case.dat',o.toString('base64'));fs.writeFileSync('.murder-case/briefing.md',j.briefing);fs.unlinkSync('.murder-case/_tmp.json');console.log('SEALED '+j.suspects.length+' suspects')"
   ```

## 4. Return ONLY this to the main session
A single line — e.g. *"A new case is ready: '<title>', 5 suspects. Briefing written."*
Never mention the murderer, the weapon, alibis, or any sheet. Output nothing else.
