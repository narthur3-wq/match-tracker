<script>
  export let title = "";
    /** rows: Array<{ player:number, att:number, scores?:number, score?:number, pct?:number, g?:number, p?:number, two?:number, t2?:number, w?:number, wide?:number, s?:number, short?:number, b?:number, blocked?:number }> */
  export let rows = [];
  export let limit = 10;   // show Top-N by default

  let showAll = false;
  $: visible = showAll ? rows : rows.slice(0, limit);
</script>

<div class="card">
  <div class="head">
    <h3 class="title">{title}</h3>
    {#if rows.length > limit}
      <button class="toggle" on:click={() => (showAll = !showAll)}>
        {showAll ? 'Show top ' + limit : 'Show all (' + rows.length + ')'}
      </button>
    {/if}
  </div>

  <table class="table">
    <thead>
      <tr>
        <th class="label">Player</th>
        <th class="num">Att</th>
        <th class="num">Scores</th>
        <th class="num">%</th>
      </tr>
    </thead>
    <tbody>
      {#if visible.length === 0}
        <tr class="empty"><td colspan="4">No shots yet</td></tr>
      {:else}
        {#each visible as r (r.player)}
          {@const att = Number(r.att) || 0}
          {@const scores = Number(r.scores ?? r.score) || 0}
          {@const pct = Number.isFinite(Number(r.pct))
            ? Math.round(Number(r.pct))
            : (att ? Math.round((scores / att) * 100) : 0)}
          {@const g = Number(r.g) || 0}
          {@const p = Number(r.p) || 0}
          {@const two = Number(r.two ?? r.t2) || 0}
          {@const w = Number(r.w ?? r.wide) || 0}
          {@const s = Number(r.s ?? r.short) || 0}
          {@const b = Number(r.b ?? r.blocked) || 0}
          </tr>
         <td class="label">{r.player === 0 ? 'TBC' : `#${r.player}`}</td>
            <td class="num">{att}</td>
            <td class="num">{scores}</td>
            <td class="num">{pct}%</td>
          </tr>
          <tr class="breakdown">
            <td colspan="4">
              <span>G:{g}</span>
              <span>P:{p}</span>
              <span>2P:{two}</span>
              <span>W:{w}</span>
              <span>S:{s}</span>
              <span>B:{b}</span>
            </td>
          </tr>
        {/each}
      {/if}
    </tbody>
  </table>
</div>

<style>
  .card { background:#fff; border:1px solid #e6ebf1; border-radius:14px; padding:12px; }
  .head { display:flex; align-items:center; justify-content:space-between; gap:8px; }
  .title { margin:4px 0 6px; font-size:16px; }
  .toggle { border:1px solid #d8e0ea; background:#f7f9fb; border-radius:999px; padding:6px 10px; font-size:12px; cursor:pointer; }
  .toggle:hover { background:#eef3f8; }

  .table { width:100%; border-collapse:collapse; table-layout:fixed; }
  .table th, .table td { padding:8px 10px; border-bottom:1px solid #eef1f4; }
  .table thead th { background:#f7f9fb; font-weight:600; }
  .table tbody tr:last-child td { border-bottom:none; }

  .label { text-align:left; }
  .num { text-align:right; }

  .empty td { text-align:center; color:#8090a0; }

  .breakdown td {
    background:#fafbfe;
    color:#6a7684;
    font-size:12px;
    border-bottom:1px solid #eef1f4;
  }
  .breakdown span { margin-right:10px; }
</style>
