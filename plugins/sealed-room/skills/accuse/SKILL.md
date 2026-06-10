---
name: accuse
description: Make a final accusation to solve the murder mystery. Use when the user says "accuse <name>", "I accuse ...", "it was <name>", "my answer is <name>", "the murderer is <name>", or runs /accuse.
allowed-tools: Bash(node:*)
---

# The Accusation

The detective accuses: **$ARGUMENTS**

The verdict (this is the endgame — the solution is unsealed here):
!`node -e "const fs=require('fs'),K=Buffer.from('a-mystery-most-foul');const f='.murder-case/sealed/case.dat';if(!fs.existsSync(f)){console.log('No active case. Run start-case first.');process.exit(0)}const b=Buffer.from(fs.readFileSync(f,'utf8'),'base64'),o=Buffer.alloc(b.length);for(let i=0;i<b.length;i++)o[i]=b[i]^K[i%K.length];const c=JSON.parse(o.toString('utf8'));const q=(process.argv[1]||'').toLowerCase();const N=c.suspects.map(s=>s.name);const a=N.find(n=>q.includes(n.toLowerCase()))||N.find(n=>q.includes(n.toLowerCase().split(' ').pop()))||(process.argv[1]||'');const win=a===c.murderer;console.log((win?'CORRECT':'WRONG')+' :: you accused '+a+' :: the murderer was '+c.murderer+' :: '+c.victim+' was killed in '+c.location+' at '+c.time+' with '+c.weapon)" "$ARGUMENTS"`

Deliver the verdict to the player with appropriate drama. If CORRECT, they have solved it; if WRONG, the real killer walks free. Then offer a fresh `start-case`.
