<script>
  // Reusable legend. Toggle sections via props.
  export let title = 'Legend';
  export let showTeam = true;       // filled = Us, outline = Opp
  export let showOutcome = true;    // Score/Miss or Win/Loss chips
  export let showCause = false;     // Forced / Unforced (turnovers)
  export let showContest = false;   // C/B/F/S (kickouts)
   export let showShots = false;     // shot outcome icons
  export let dense = false;         // tighter spacing
  export let outcomeMode = 'wl';    // 'wl' | 'score'

  // Standardized colors (match markers on the Pitch)
  const COL = {
    score: '#16a34a',   // green
    miss: '#dc2626',    // red
    neutral: '#aab2bd', // grey
    stroke: '#6b7c93',  // outline for shapes
    primary: '#0660aa'  // club blue
  };
</script>

<div class="legend" class:dense aria-label="Legend">
  <h4 class="h">{title}</h4>

  {#if showOutcome}
    <div class="row">
      {#if outcomeMode === 'score'}
        <span class="chip" style="--bg:{COL.score}"></span> <span>Score</span>
        <span class="chip" style="--bg:{COL.miss}"></span> <span>Miss</span>
      {:else}
        <span class="chip" style="--bg:{COL.score}"></span> <span>Win</span>
        <span class="chip" style="--bg:{COL.miss}"></span> <span>Loss</span>
        <span class="chip" style="--bg:{COL.neutral}"></span> <span>Neutral</span>
      {/if}
    </div>
  {/if}

  {#if showTeam}
    <div class="row">
      <span class="chip team" data-team="us"></span> <span>Us</span>
      <span class="chip team" data-team="opp"></span> <span>Opp</span>
    </div>
  {/if}

  {#if showCause}
    <div class="row">
      <span class="label">F</span><span>Forced loss</span>
      <span class="label">U</span><span>Unforced loss</span>
      <span class="label">?</span><span>Unknown</span>
    </div>
  {/if}

  {#if showContest}
    <div class="row">
      <span class="label">C</span><span>Clean</span>
      <span class="label">B</span><span>Break</span>
      <span class="label">F</span><span>Foul</span>
      <span class="label">S</span><span>Sideline</span>
    </div>
  {/if}

  {#if showShots}
     <div class="row shots">
      <span class="shot-icon circle" aria-hidden="true"></span><span>Goal</span>
      <span class="shot-icon dot" aria-hidden="true"></span><span>Point</span>
      <span class="shot-icon triangle" aria-hidden="true"></span><span>2P</span>
      <span class="shot-icon x" aria-hidden="true"></span><span>Wide</span>
      <span class="shot-icon square" aria-hidden="true"></span><span>Short</span>
      <span class="shot-icon star" aria-hidden="true"></span><span>Blocked</span>
    </div>
  {/if}
</div>

<style>
  .legend { display:grid; gap:8px; color:#5f7083; }
  .legend.dense { gap:6px; }
  .h { margin:0; font-size:14px; font-weight:700; color:#213042; }

  .row {
    display:grid;
    grid-template-columns: auto 1fr auto 1fr auto 1fr auto 1fr;
    align-items:center;
    gap:8px 10px;
    font-size:13px;
  }
  .row.shots {
    grid-template-columns: auto 1fr auto 1fr auto 1fr auto 1fr auto 1fr auto 1fr;
  }

  .chip {
    --bg: #aab2bd;
    display:inline-block;
    width:12px; height:12px;
    border-radius:999px;
    background:var(--bg);
    border:1px solid #e2e6ea;
    margin-right:2px;
  }
    .chip.team {
    border:2px solid #0660aa;
    background:#0660aa;
  }
    .chip.team[data-team="opp"] { background:#fff; }

  .label {
    display:inline-grid; place-items:center;
    min-width:18px; height:18px;
    padding:0 6px;
    border-radius:6px;
    border:1px solid #d8e0ea;
    background:#f7f9fb;
    font-size:11px; font-weight:700;
    color:#213042;
  }

  .shot-icon {
    position:relative;
    display:inline-block;
    width:14px; height:14px;
    color:#213042;
  }
  .shot-icon.circle { border:2px solid currentColor; border-radius:50%; }
  .shot-icon.dot    { background:currentColor; border-radius:50%; }
  .shot-icon.triangle {
    width:0; height:0;
    border-left:7px solid transparent;
    border-right:7px solid transparent;
    border-bottom:14px solid currentColor;
  }
  .shot-icon.x::before,
  .shot-icon.x::after {
    content:"";
    position:absolute;
    top:0; left:6px;
    width:2px; height:14px;
    background:currentColor;
  }
  .shot-icon.x::before { transform:rotate(45deg); }
  .shot-icon.x::after  { transform:rotate(-45deg); }
  .shot-icon.square { border:2px solid currentColor; }
  .shot-icon.star {
    background:currentColor;
    clip-path:polygon(50% 0%,61% 35%,98% 35%,68% 57%,79% 91%,50% 70%,21% 91%,32% 57%,2% 35%,39% 35%);
  }
</style>
