---
name: interrogate
description: Question a suspect in the current murder mystery. Use when the user says "interrogate <name>", "ask <name> about ...", "question <suspect>", "talk to <suspect>", "press <suspect>", or runs /interrogate.
allowed-tools: Bash(node:*)
context: fork
---

# Interrogate a Suspect

You are in a **forked context**, so the suspect's secret sheet never reaches the main game session. Play the scene; return only what the suspect says out loud.

The detective says: **$ARGUMENTS**

Your secret character sheet — decoded only here. (Questioning a suspect counts as **one action** toward your score.)
!`node -e "const fs=require('fs'),K=Buffer.from('a-mystery-most-foul');const f='.murder-case/sealed/case.dat';if(!fs.existsSync(f)){console.log('NO_CASE');process.exit(0)}const b=Buffer.from(fs.readFileSync(f,'utf8'),'base64'),o=Buffer.alloc(b.length);for(let i=0;i<b.length;i++)o[i]=b[i]^K[i%K.length];const c=JSON.parse(o.toString('utf8'));const q=(process.argv[1]||'').toLowerCase();const N=Object.keys(c.sheets);const m=N.find(n=>q.includes(n.toLowerCase()))||N.find(n=>q.includes(n.toLowerCase().split(' ').pop()))||N.find(n=>n.toLowerCase().includes(q));if(!m){console.log('NO_MATCH. Suspects: '+N.join(', '));process.exit(0)}try{const af='.murder-case/actions';let a=0;try{a=parseInt(fs.readFileSync(af,'utf8'))||0}catch(e){}fs.writeFileSync(af,String(a+1))}catch(e){}const s=c.sheets[m];console.log('You are '+s.identity+'.\nDemeanour: '+s.demeanor+'.\nStated alibi: '+s.alibi_claim+'\nYou know: '+(s.knows||[]).join(' ')+'\nConceal or deny: '+((s.conceal&&s.conceal.length)?s.conceal.join(' '):'nothing')+'\nYour tell: '+s.tell+'\nUnder pressure: '+s.pressure+'\nYou do NOT know who the murderer is.')" "$ARGUMENTS"`

Now become that suspect and answer the detective's question:
- Speak in **first person, in character**, matching their demeanour.
- If the sheet says to conceal or deny something, **lie convincingly** — never break character.
- If the question lands on your **tell**, let it slip *subtly* (a hesitation, a too-quick denial). Don't announce it.
- You do **not** know who the murderer is.

Return **only** the spoken dialogue (optionally one short stage direction like *(she glances away)*). Nothing else. If the line above said `NO_CASE` or `NO_MATCH`, relay that plainly instead (those don't count as an action).

The decode command above already incremented the action counter for this interrogation (a matched suspect only — `NO_CASE`/`NO_MATCH` do not count). **Do not** write to or otherwise touch `.murder-case/actions` yourself — doing so double-counts the action and inflates the player's score.
