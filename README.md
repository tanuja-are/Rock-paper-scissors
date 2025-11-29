<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Rock · Paper · Scissors — Play!</title>
  <style>
    :root{--bg:#0f1724;--card:#0b1220;--accent:#7c3aed;--muted:#94a3b8;--win:#10b981;--lose:#ef4444}
    *{box-sizing:border-box}
    html,body{height:100%}
    body{margin:0;font-family:Inter, ui-sans-serif, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;color:#e6eef8;background:linear-gradient(180deg,#071129 0%, #081127 60%);display:flex;align-items:center;justify-content:center;padding:28px}
    .wrap{wid\th:100%;max-width:820px;background:linear-gradient(180deg,rgba(255,255,255,0.02),rgba(255,255,255,0.01));border-radius:14px;padding:22px;box-shadow:0 10px 30px rgba(2,6,23,0.6);border:1px solid rgba(255,255,255,0.03)}
    header{display:flex;align-items:center;justify-content:space-between;gap:12px}
    h1{margin:0;font-size:20px;letter-spacing:0.4px}
    p.lead{margin:4px 0 0;color:var(--muted);font-size:13px}

    .game{display:grid;grid-template-columns:1fr 320px;gap:18px;margin-top:18px}
    @media (max-width:820px){.game{grid-template-columns:1fr}}

    .panel{background:rgba(255,255,255,0.02);padding:18px;border-radius:10px}

    .choices{display:flex;gap:12px;align-items:center;justify-content:center}
    .choice{flex:1;min-width:78px;padding:14px;border-radius:10px;background:linear-gradient(180deg,rgba(255,255,255,0.01),rgba(0,0,0,0.08));text-align:center;border:1px solid rgba(255,255,255,0.03);cursor:pointer;user-select:none;transition:transform .12s ease, box-shadow .12s ease}
    .choice:active{transform:translateY(2px)}
    .choice .emoji{font-size:34px;display:block}
    .choice .label{font-size:13px;margin-top:8px;color:var(--muted)}

    .choice[data-state="win"]{box-shadow:0 10px 30px rgba(16,185,129,0.12);border-color:rgba(16,185,129,0.22)}
    .choice[data-state="lose"]{box-shadow:0 10px 30px rgba(239,68,68,0.08);border-color:rgba(239,68,68,0.18)}
    .choice[data-state="tie"]{box-shadow:0 6px 18px rgba(148,163,184,0.04);border-color:rgba(148,163,184,0.06)}

    .scoreboard{display:flex;flex-direction:column;gap:10px}
    .scores{display:flex;gap:12px;align-items:center;justify-content:space-between}
    .score{flex:1;padding:10px;border-radius:8px;background:linear-gradient(180deg,rgba(255,255,255,0.01),rgba(0,0,0,0.06));text-align:center}
    .score h3{margin:0;font-size:12px;color:var(--muted)}
    .score p{margin:6px 0 0;font-size:26px}

    .result{display:flex;align-items:center;gap:12px;justify-content:space-between;padding:12px;border-radius:8px;background:linear-gradient(180deg,rgba(255,255,255,0.01),rgba(0,0,0,0.04));font-weight:600}
    .result .text{font-size:15px}
    .muted{color:var(--muted);font-weight:500}

    .controls{display:flex;gap:8px;align-items:center;justify-content:flex-end}
    button.btn{appearance:none;border:0;padding:9px 12px;border-radius:8px;background:#0b1220;color:#dbeafe;cursor:pointer;font-weight:600}
    button.ghost{background:transparent;border:1px solid rgba(255,255,255,0.03);color:var(--muted)}

    footer.note{margin-top:12px;color:var(--muted);font-size:12px;text-align:center}

    /* shake animation for result */
    @keyframes pop {0%{transform:scale(.96)}50%{transform:scale(1.03)}100%{transform:scale(1)}}
    .flash{animation:pop .28s ease}

  </style>
</head>
<body>
  <div class="wrap" role="application" aria-labelledby="title">
    <header>
      <div>
        <h1 id="title">Rock · Paper · Scissors</h1>
        <p class="lead">Click an option or press R / P / S — first to reach the target score wins.</p>
      </div>
      <div style="text-align:right">
        <label for="target" class="muted">Target score</label>
        <div style="display:flex;gap:8px;align-items:center;justify-content:flex-end">
          <input id="target" type="number" min="1" max="20" value="5" style="width:64px;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:inherit;font-weight:600" />
          <button id="reset" class="btn ghost" title="Reset game">Reset</button>
        </div>
      </div>
    </header>

    <main class="game" aria-live="polite">
      <section class="panel">
        <div class="choices" role="list" aria-label="Options">
          <div class="choice" role="listitem" tabindex="0" data-choice="rock" aria-label="Rock (R)" title="Rock — R">
            <div class="emoji">✊</div>
            <div class="label">Rock</div>
          </div>
          <div class="choice" role="listitem" tabindex="0" data-choice="paper" aria-label="Paper (P)" title="Paper — P">
            <div class="emoji">✋</div>
            <div class="label">Paper</div>
          </div>
          <div class="choice" role="listitem" tabindex="0" data-choice="scissors" aria-label="Scissors (S)" title="Scissors — S">
            <div class="emoji">✌️</div>
            <div class="label">Scissors</div>
          </div>
        </div>

        <div style="margin-top:14px">
          <div class="result" id="result">
            <div class="text">Make your move</div>
            <div class="muted" id="round">Round 0</div>
          </div>
        </div>

        <div style="margin-top:12px;font-size:13px;color:var(--muted)">
          <strong>Tip:</strong> Use keyboard keys <code>R</code>, <code>P</code>, <code>S</code> or click the tiles. Press <code>Space</code> to play again after a round.
        </div>
      </section>

      <aside class="panel scoreboard" aria-label="Scoreboard">
        <div class="scores">
          <div class="score">
            <h3>You</h3>
            <p id="playerScore">0</p>
          </div>
          <div class="score">
            <h3>Computer</h3>
            <p id="computerScore">0</p>
          </div>
        </div>

        <div style="margin-top:8px">
          <div style="display:flex;gap:8px;align-items:center;justify-content:space-between">
            <div class="muted">Last picks</div>
            <div class="muted" id="lastPicks">—</div>
          </div>
        </div>

        <div style="margin-top:12px">
          <div class="controls">
            <button id="undo" class="btn ghost" title="Undo last round">Undo</button>
            <button id="auto" class="btn" title="Toggle auto-mode">Auto: Off</button>
          </div>
        </div>

        <div style="margin-top:10px;display:flex;gap:8px">
          <button id="newMatch" class="btn">New Match</button>
          <button id="share" class="btn ghost">Copy Result</button>
        </div>

      </aside>
    </main>

    <footer class="note">Created for quick fun — accessible, keyboard-friendly, and mobile-ready.</footer>
  </div>

  <script>
    // Game logic
    const choices = ['rock','paper','scissors'];
    const beats = {rock:'scissors', paper:'rock', scissors:'paper'};

    const el = (sel)=>document.querySelector(sel);
    const els = (sel)=>Array.from(document.querySelectorAll(sel));

    let playerScore = 0, computerScore = 0, round = 0;
    let last = null; // {player,computer,result}
    let history = [];
    let autoMode = false;

    const playerScoreEl = el('#playerScore');
    const computerScoreEl = el('#computerScore');
    const resultEl = el('#result .text');
    const roundEl = el('#round');
    const lastPicksEl = el('#lastPicks');
    const targetInput = el('#target');
    const autoBtn = el('#auto');

    function compPick(){
      return choices[Math.floor(Math.random()*choices.length)];
    }

    function play(player){
      const computer = compPick();
      let outcome = 'tie';
      if(beats[player] === computer) outcome = 'win';
      else if(beats[computer] === player) outcome = 'lose';

      round += 1;
      last = {player,computer,outcome,round};
      history.push(last);

      if(outcome === 'win') playerScore++;
      else if(outcome === 'lose') computerScore++;

      updateUI(outcome);
      checkMatchEnd();
    }

    function updateUI(outcome){
      playerScoreEl.textContent = playerScore;
      computerScoreEl.textContent = computerScore;
      roundEl.textContent = `Round ${round}`;
      const p = last.player, c = last.computer;
      lastPicksEl.textContent = `${p.toUpperCase()} vs ${c.toUpperCase()}`;

      // result text
      if(outcome === 'win') resultEl.textContent = `You win — ${last.player} beats ${last.computer}`;
      else if(outcome === 'lose') resultEl.textContent = `You lose — ${last.computer} beats ${last.player}`;
      else resultEl.textContent = `Tie — both chose ${last.player}`;

      flashChoices(outcome);
      el('#result').classList.remove('flash');
      void el('#result').offsetWidth; // reflow
      el('#result').classList.add('flash');
    }

    function flashChoices(outcome){
      els('.choice').forEach(c=>{ c.removeAttribute('data-state'); });
      const playerTile = els(`.choice`).find(d=>d.dataset.choice===last.player);
      const compTile = els(`.choice`).find(d=>d.dataset.choice===last.computer);
      const pState = outcome === 'win' ? 'win' : outcome === 'lose' ? 'lose' : 'tie';
      const cState = outcome === 'win' ? 'lose' : outcome === 'lose' ? 'win' : 'tie';
      if(playerTile) playerTile.setAttribute('data-state',pState);
      if(compTile) compTile.setAttribute('data-state',cState);
    }

    function checkMatchEnd(){
      const target = Number(targetInput.value) || 5;
      if(playerScore >= target || computerScore >= target){
        const winner = playerScore>computerScore ? 'You' : 'Computer';
        el('#result .text').textContent = `${winner} won the match! Final: ${playerScore} — ${computerScore}`;
        // lock choices
        els('.choice').forEach(c=>c.setAttribute('aria-disabled','true'));
      }
    }

    // UI wiring
    els('.choice').forEach(tile=>{
      tile.addEventListener('click',()=>{
        if(tile.getAttribute('aria-disabled')==='true') return;
        play(tile.dataset.choice);
      });
      tile.addEventListener('keydown',(e)=>{
        if(e.key === 'Enter' || e.key === ' ') { e.preventDefault(); tile.click(); }
      });
    });

    el('#reset').addEventListener('click',resetGame);
    el('#newMatch').addEventListener('click',newMatch);
    el('#undo').addEventListener('click',undo);
    el('#share').addEventListener('click',copySummary);
    autoBtn.addEventListener('click',toggleAuto);

    // keyboard shortcuts
    window.addEventListener('keydown',(e)=>{
      if(e.repeat) return;
      const k = e.key.toLowerCase();
      if(k === 'r') simulate('rock');
      if(k === 'p') simulate('paper');
      if(k === 's') simulate('scissors');
      if(k === ' '){ // space to play again (re-run last player pick) when not at match end
        if(last && !(playerScore >= Number(targetInput.value) || computerScore >= Number(targetInput.value))) { play(last.player); }
      }
    });

    function simulate(choice){
      const tile = els('.choice').find(d=>d.dataset.choice===choice);
      if(tile) tile.click();
    }

    function resetGame(){
      playerScore = 0; computerScore = 0; round = 0; history = []; last = null;
      playerScoreEl.textContent = '0'; computerScoreEl.textContent = '0'; roundEl.textContent = 'Round 0'; lastPicksEl.textContent = '—';
      resultEl.textContent = 'Make your move';
      els('.choice').forEach(c=>{ c.removeAttribute('data-state'); c.removeAttribute('aria-disabled'); });
    }

    function newMatch(){
      resetGame();
      // leave target as-is
    }

    function undo(){
      if(history.length === 0) return;
      const lastEntry = history.pop();
      // revert score
      if(lastEntry.outcome === 'win') playerScore--;
      else if(lastEntry.outcome === 'lose') computerScore--;
      round = lastEntry.round - 1;
      last = history[history.length-1] || null;
      updateAfterUndo();
    }

    function updateAfterUndo(){
      playerScoreEl.textContent = playerScore;
      computerScoreEl.textContent = computerScore;
      roundEl.textContent = `Round ${round}`;
      if(last){ lastPicksEl.textContent = `${last.player.toUpperCase()} vs ${last.computer.toUpperCase()}`; el('#result .text').textContent = `Last: ${last.player} vs ${last.computer} — ${last.outcome}`; flashChoices(last.outcome);} else { lastPicksEl.textContent = '—'; el('#result .text').textContent = 'Make your move'; els('.choice').forEach(c=>c.removeAttribute('data-state')); }
      els('.choice').forEach(c=>c.removeAttribute('aria-disabled')); // allow play again
    }

    function copySummary(){
      const txt = `RPS result — You ${playerScore} : ${computerScore} Computer (target ${targetInput.value})`;
      navigator.clipboard?.writeText(txt).then(()=>{
        const orig = el('#share').textContent;
        el('#share').textContent = 'Copied!';
        setTimeout(()=>el('#share').textContent = orig,1200);
      }).catch(()=>alert(txt));
    }

    function toggleAuto(){
      autoMode = !autoMode;
      autoBtn.textContent = `Auto: ${autoMode? 'On' : 'Off'}`;
      if(autoMode) startAuto();
    }

    let autoInterval = null;
    function startAuto(){
      if(!autoMode){ clearInterval(autoInterval); autoInterval=null; return; }
      if(autoInterval) return;
      autoInterval = setInterval(()=>{
        if(playerScore >= Number(targetInput.value) || computerScore >= Number(targetInput.value)) { clearInterval(autoInterval); autoInterval=null; autoMode=false; autoBtn.textContent='Auto: Off'; return; }
        const choice = choices[Math.floor(Math.random()*choices.length)];
        play(choice);
      }, 700);
    }

    // initialize
    resetGame();

  </script>
</body>
</html>
