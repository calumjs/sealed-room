---
name: examine
description: Examine a room, the body, or an object for physical clues in the current murder mystery. Use when the user says "examine <place>", "search <room>", "inspect <object>", "look at <place>", "check the body", or runs /examine.
allowed-tools: Bash(node:*)
---

# Examine the Scene

The detective examines: **$ARGUMENTS** — searching the scene counts as **one action** toward your score.

What is found:
!`node -e "const fs=require('fs'),K=Buffer.from('a-mystery-most-foul');const f='.murder-case/sealed/case.dat';if(!fs.existsSync(f)){console.log('No active case. Run start-case first.');process.exit(0)}const b=Buffer.from(fs.readFileSync(f,'utf8'),'base64'),o=Buffer.alloc(b.length);for(let i=0;i<b.length;i++)o[i]=b[i]^K[i%K.length];const c=JSON.parse(o.toString('utf8'));try{const af='.murder-case/actions';let a=0;try{a=parseInt(fs.readFileSync(af,'utf8'))||0}catch(e){}fs.writeFileSync(af,String(a+1))}catch(e){}const p=(process.argv[1]||'').toLowerCase().replace(/^the /,'').trim();const ks=Object.keys(c.clues);const hit=ks.find(k=>k.toLowerCase().replace(/^the /,'')===p)||ks.find(k=>k.toLowerCase().includes(p)||p.includes(k.toLowerCase().replace(/^the /,'')));console.log(hit?c.clues[hit]:'You search carefully, but find nothing of note here.')" "$ARGUMENTS"`

Narrate that finding in atmospheric, period prose. Do not invent clues beyond it. Never read `.murder-case/sealed/`.

The command above already incremented the action counter for this search. **Do not** write to or otherwise touch `.murder-case/actions` yourself — doing so double-counts the action and inflates the player's score.
