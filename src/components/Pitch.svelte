<script>
  import { toDisplayXY } from '$lib/coords.js';
  import { orientation_left } from '$lib/stores.js';

   // [{ x, y, class, label, dataColor, shape, team, savedOrientationLeft }]
  export let marks = [];
  export let savedOrientationLeft = false; // fallback for legacy callers

  const W = 145, H = 90;
  const mid = W / 2;
  const thin = 0.75, med = 0.9, strong = 1.1;
</script>

<div class="pitch" role="img" aria-label="Gaelic football pitch">
  <svg class="svg" viewBox={`0 0 ${W} ${H}`} aria-hidden="true">
    <!-- perimeter -->
    <rect x="1" y="1" width={W-2} height={H-2} fill="none" stroke="var(--pitch-strong)" stroke-width={strong} rx="1.5" />

    <!-- halfway (solid, thin) -->
    <line x1={mid} y1="1" x2={mid} y2={H-1} stroke="var(--pitch-line)" stroke-width={thin} />

    <!-- 13/20/45 verticals -->
    {#each [13, 20, 45] as d}
      <line x1={d} y1="1" x2={d} y2={H-1} stroke="var(--pitch-line)" stroke-width={thin} />
      <line x1={W-d} y1="1" x2={W-d} y2={H-1} stroke="var(--pitch-line)" stroke-width={thin} />
    {/each}

    <!-- rectangles -->
    <rect x="0.8" y={H/2-9.5} width="6.5" height="19" fill="none" stroke="var(--pitch-strong)" stroke-width={thin}/>
    <rect x="0.8" y={H/2-19}  width="13"  height="38" fill="none" stroke="var(--pitch-strong)" stroke-width={thin}/>
    <rect x={W-6.5-0.8} y={H/2-9.5} width="6.5" height="19" fill="none" stroke="var(--pitch-strong)" stroke-width={thin}/>
    <rect x={W-13-0.8}  y={H/2-19}  width="13"  height="38" fill="none" stroke="var(--pitch-strong)" stroke-width={thin}/>

    <!-- 40m arcs -->
    <path d={`M 0 5 A 40 40 0 0 1 40 45`} fill="none" stroke="var(--pitch-strong)" stroke-width={med}/>
    <path d={`M 40 45 A 40 40 0 0 1 0 85`} fill="none" stroke="var(--pitch-strong)" stroke-width={med}/>
    <path d={`M ${W} 5 A 40 40 0 0 0 ${W-40} 45`} fill="none" stroke="var(--pitch-strong)" stroke-width={med}/>
    <path d={`M ${W-40} 45 A 40 40 0 0 0 ${W} 85`} fill="none" stroke="var(--pitch-strong)" stroke-width={med}/>

    <!-- 13m arcs -->
    <path d={`M 20 32 A 13 13 0 0 1 20 58`} fill="none" stroke="var(--pitch-strong)" stroke-width={thin}/>
    <path d={`M ${W-20} 32 A 13 13 0 0 0 ${W-20} 58`} fill="none" stroke="var(--pitch-strong)" stroke-width={thin}/>
  </svg>

  {#each marks as m, i}
   {@const p = toDisplayXY(
      m.x ?? 0.5,
      m.y ?? 0.5,
      (m.savedOrientationLeft ?? savedOrientationLeft) ?? true,
      $orientation_left
    )}
    {#key i}
      
      <div class="ovl {m.class ?? ''}" style="left:{p.x*100}%; top:{p.y*100}%;">

        <!-- Bigger glyphs + 'with-label' class for legibility -->
        <div
          class="mark {m.label ? 'with-label' : ''}"
          data-color={m.dataColor ?? null}
          data-shape={m.shape ?? null}
          data-team={m.team ?? null}          
          aria-hidden="true"
        >
          <span>{m.label ?? ''}</span>
        </div>
    </div>
    {/key}
  {/each}
</div>

<style>
  .pitch { position: relative; width: 100%; }
  .svg   { display: block; width: 100%; height: auto; }

  .ovl {
    position: absolute;
    transform: translate(-50%, -50%);
    pointer-events: none;
  }

  /* === Glyphs ========================================================= */
  .mark {
    /* larger base size */
    width: 18px; height: 18px;
    border-radius: 999px;
    border: 2.5px solid #fff;               /* white halo */
    background: var(--accent);              /* unified brand blue */
    display: grid; place-items: center;
    font: 800 11px/1 system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
    color: #fff;
        position: relative;
    --shot-color: var(--accent);
/* subtle drop shadow + hairline ring for contrast on pitch lines */
    box-shadow: 0 1.5px 3px rgba(0,0,0,.25), 0 0 0 1px rgba(0,0,0,.08);
  }
  .mark.with-label { width: 22px; height: 22px; }
  .mark > span {
    /* make letters/numbers pop on any background */
    text-shadow: 0 1px 1px rgba(0,0,0,.55);
  }

  /* outcomes (kickouts use data-color) */
  .mark[data-color="win"]     { background: var(--win); }
  .mark[data-color="loss"]    { background: var(--loss); }
  .mark[data-color="neutral"] { background: var(--neutral); }

  /* shot colors */
  .mark[data-color="score"] { --shot-color: var(--win); }
  .mark[data-color="miss"]  { --shot-color: var(--loss); }

  .mark[data-team="us"] {
    background: var(--shot-color);
    border-color: #fff;
    color: #fff;
  }

  .mark[data-team="opp"] {
    background: transparent;
    border-color: var(--shot-color);
    color: var(--shot-color);
  }

  /* Shot outcome shapes */
  .mark[data-shape="goal"]    { border-radius: 999px; }
  .mark[data-shape="point"]   { border-radius: 3px; }
  .mark[data-shape="wide"]    { transform: rotate(45deg); border-radius: 3px; }
  .mark[data-shape="wide"] > span { transform: rotate(-45deg); }
  .mark[data-shape="short"]   { clip-path: polygon(50% 0, 100% 100%, 0 100%); }
  .mark[data-shape="blocked"]::before,
  .mark[data-shape="blocked"]::after {
    content: '';
    position: absolute;
    width: 70%;
    height: 2px;
    background: currentColor;
    top: 50%;
    left: 15%;
  }
  .mark[data-shape="blocked"]::before { transform: rotate(45deg); }
  .mark[data-shape="blocked"]::after  { transform: rotate(-45deg); }
  .mark[data-shape="twoPt"] {
    background: transparent;
    border-color: var(--shot-color);
    color: var(--shot-color);
    box-shadow: 0 0 0 3px var(--shot-color);
  }  
/* make kickout glyphs a touch bigger still */
  .ovl.ko .mark { width: 22px; height: 22px; }
</style>
