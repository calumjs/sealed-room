---
name: scores
description: Show the sealed-room high-scores table — past games, their scores, and how they were solved. Use when the user says "scores", "high scores", "leaderboard", "show the scores", "my stats", "score table", or runs /scores.
allowed-tools: Bash(node:*)
---

# High Scores

Viewing the leaderboard is free — it does not count as an action.

!`node -e "const fs=require('fs');const f='.murder-case/highscores.json';let arr=[];try{arr=JSON.parse(fs.readFileSync(f,'utf8'))}catch(e){}if(!arr.length){console.log('No games on record yet. Solve a mystery first!');process.exit(0)}arr.sort((x,y)=>(x.result===y.result)?x.score-y.score:(x.result==='solved'?-1:1));arr.forEach((r,i)=>console.log((i+1)+'. ['+r.date+']  '+(r.result==='solved'?'SOLVED':'WRONG ')+'  score '+r.score+'  —  '+r.mystery+'  ::  '+r.comment))"`

Present this as a tidy leaderboard for the player. Solved games rank above failed ones; within each group, a **lower score (fewer actions) ranks higher**. If it says no games yet, invite them to play with **start a murder mystery**.
