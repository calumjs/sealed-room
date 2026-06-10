---
name: accuse
description: Make a final accusation to solve the murder mystery. Use when the user says "accuse <name>", "I accuse ...", "it was <name>", "my answer is <name>", "the murderer is <name>", or runs /accuse.
allowed-tools: Bash(node:*)
---

# The Accusation

The detective accuses: **$ARGUMENTS**

The verdict — the solution is unsealed here, and your **score** is the number of actions (interrogations + searches) you took *before* accusing:
!`node -e "const fs=require('fs'),K=Buffer.from('a-mystery-most-foul');const f='.murder-case/sealed/case.dat';if(!fs.existsSync(f)){console.log('No active case. Run start-case first.');process.exit(0)}const b=Buffer.from(fs.readFileSync(f,'utf8'),'base64'),o=Buffer.alloc(b.length);for(let i=0;i<b.length;i++)o[i]=b[i]^K[i%K.length];const c=JSON.parse(o.toString('utf8'));let acts=0;try{acts=parseInt(fs.readFileSync('.murder-case/actions','utf8'))||0}catch(e){}const q=(process.argv[1]||'').toLowerCase();const N=c.suspects.map(s=>s.name);const a=N.find(n=>q.includes(n.toLowerCase()))||N.find(n=>q.includes(n.toLowerCase().split(' ').pop()))||(process.argv[1]||'');const win=a===c.murderer;console.log((win?'CORRECT':'WRONG')+' :: you accused '+a+' :: the murderer was '+c.murderer+' :: '+c.victim+' was killed in '+c.location+' at '+c.time+' with '+c.weapon+' :: score(actions): '+acts)" "$ARGUMENTS"`

Then:

1. **Deliver the verdict with drama.** If `CORRECT`, they solved it; if `WRONG`, the real killer walks free. State the **score** (the action count above — fewer is better) and add a cheeky rank: 0–3 = *"Master Detective"*, 4–6 = *"Sharp"*, 7+ = *"Thorough"*.

2. **Log it to the high-scores table.** Compose a short one-line `comment` on how you cracked it (the key deduction — e.g. *"His boathouse alibi couldn't survive the missing gloves."*). Then append the record (replace each bracketed part; keep each as **one quoted argument**):

   ```
   node -e "const fs=require('fs');const [mystery,score,result,accused,comment]=process.argv.slice(1);const f='.murder-case/highscores.json';let arr=[];try{arr=JSON.parse(fs.readFileSync(f,'utf8'))}catch(e){}arr.push({date:new Date().toISOString().slice(0,10),mystery,score:Number(score),result,accused,comment});fs.writeFileSync(f,JSON.stringify(arr,null,2));console.log('Logged to .murder-case/highscores.json — '+arr.length+' game(s) on record.')" "<mystery title>" "<score number>" "<solved|wrong>" "<who you accused>" "<one-line how-you-solved-it>"
   ```

3. Offer them a fresh **start a murder mystery**, or **scores** to see the leaderboard.
