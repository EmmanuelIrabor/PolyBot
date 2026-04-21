<script lang="ts">
  let autoMode = false;
  let executing = false;
  let logs: { time: string; msg: string; type: 'info' | 'success' | 'warn' | 'error' }[] = [
    { time: '14:22:01', msg: 'Bot initialized. Watching 5 markets.', type: 'info' },
    { time: '14:22:45', msg: 'Signal: BTC/USD YES @ 0.62 — confidence 84%', type: 'info' },
    { time: '14:23:10', msg: 'Trade placed: BUY YES "Will BTC exceed $100k?" — $200', type: 'success' },
    { time: '14:25:00', msg: 'Signal: Fed Rate Cut NO @ 0.73 — confidence 71%', type: 'info' },
    { time: '14:25:14', msg: 'Trade placed: BUY NO "Will Fed cut rates in May?" — $150', type: 'success' },
    { time: '14:31:00', msg: 'Market closed before execution threshold — skipped.', type: 'warn' },
  ];

  type ActiveTrade = {
    question: string; action: string; amount: number;
    entryPrice: number; currentPrice: number; pnl: number;
  };

  let activeTrades: ActiveTrade[] = [
    { question: 'Will BTC exceed $100k before June?', action: 'YES', amount: 200, entryPrice: 0.62, currentPrice: 0.65, pnl: 9.68 },
    { question: 'Will the Fed cut rates in May 2025?', action: 'NO',  amount: 150, entryPrice: 0.73, currentPrice: 0.76, pnl: 6.16 },
  ];

  function toggleAuto() {
    autoMode = !autoMode;
    pushLog(
      autoMode ? 'AutoBot ENABLED — scanning markets for trade signals...' : 'AutoBot DISABLED — manual mode active.',
      autoMode ? 'success' : 'warn'
    );
  }

  function pushLog(msg: string, type: 'info' | 'success' | 'warn' | 'error') {
    logs = [{ time: new Date().toTimeString().slice(0, 8), msg, type }, ...logs];
  }

  async function runOnce() {
    executing = true;
    pushLog('Manual scan triggered...', 'info');
    await new Promise(r => setTimeout(r, 1200));
    pushLog('Fetching Polymarket data...', 'info');
    await new Promise(r => setTimeout(r, 800));
    pushLog('Claude AI analyzing top signals...', 'info');
    await new Promise(r => setTimeout(r, 1000));
    pushLog('No high-confidence trades above threshold right now.', 'warn');
    executing = false;
  }

  function logColor(t: string) {
    if (t === 'success') return 'var(--accent-green)';
    if (t === 'warn') return 'var(--accent-yellow)';
    if (t === 'error') return 'var(--accent-red)';
    return 'var(--text-secondary)';
  }
  function logPrefix(t: string) {
    return t === 'success' ? '✓' : t === 'warn' ? '!' : t === 'error' ? '✗' : '›';
  }

  $: totalPnl = activeTrades.reduce((s, t) => s + t.pnl, 0);
</script>

<svelte:head><title>AutoBot · PolyBot</title></svelte:head>

<!-- PnL summary chips — always top -->
<div class="flex flex-wrap gap-2 mb-4">
  {#each [
    ['Open P&L', `${totalPnl >= 0 ? '+' : ''}$${totalPnl.toFixed(2)}`, totalPnl >= 0 ? 'var(--accent-green)' : 'var(--accent-red)'],
    ['Positions', activeTrades.length.toString(), 'var(--accent-blue)'],
    ['Today\'s Trades', '2', 'var(--text-secondary)'],
  ] as [label, val, color]}
    <div class="px-3 py-2 rounded-lg flex items-center gap-2"
         style="background: var(--bg-card); border: 1px solid var(--border);">
      <span class="text-sm font-mono font-bold" style="color: {color};">{val}</span>
      <span class="text-xs" style="color: var(--text-muted);">{label}</span>
    </div>
  {/each}
</div>

<!-- Auto toggle card -->
<div class="p-4 rounded-xl mb-4 card-border transition-all"
     style="background: var(--bg-card); {autoMode ? 'border-color: var(--accent-green); box-shadow: 0 0 30px #00e5a012;' : ''}">

  <div class="flex items-center justify-between gap-4 mb-4">
    <div class="flex-1 min-w-0">
      <div class="text-sm font-semibold" style="color: var(--text-primary);">Autonomous Trading</div>
      <div class="text-xs mt-0.5" style="color: var(--text-muted);">
        {autoMode ? 'Bot is scanning and executing automatically' : 'Manual mode — review and approve each trade'}
      </div>
    </div>
    <button onclick={toggleAuto}
            class="relative w-12 h-6 rounded-full flex-shrink-0 transition-colors"
            style="background: {autoMode ? 'var(--accent-green)' : 'var(--border-bright)'};"
            aria-label="Toggle auto mode">
      <span class="absolute top-0.5 w-5 h-5 bg-white rounded-full shadow transition-all duration-300"
            style="left: {autoMode ? '26px' : '2px'};"></span>
    </button>
  </div>

  <!-- Config grid — 2 cols on mobile, 4 on sm+ -->
  <div class="grid grid-cols-2 sm:grid-cols-4 gap-2 mb-4">
    {#each [['Min Confidence','65%'],['Max/Trade','$250'],['Daily Limit','$1,000'],['Risk Level','Medium']] as [label, val]}
      <div class="p-2.5 rounded-lg text-center" style="background: var(--bg-surface); border: 1px solid var(--border);">
        <div class="text-xs font-mono font-semibold" style="color: var(--text-primary);">{val}</div>
        <div class="text-xs mt-0.5 leading-tight" style="color: var(--text-muted);">{label}</div>
      </div>
    {/each}
  </div>

  <!-- Actions -->
  <div class="flex flex-wrap gap-2">
    <button onclick={runOnce}
            disabled={executing || autoMode}
            class="text-xs px-4 py-2 rounded-lg font-medium transition-all disabled:opacity-40"
            style="background: var(--bg-hover); color: var(--text-secondary); border: 1px solid var(--border);">
      {executing ? '⟳ Scanning...' : 'Run Scan Once'}
    </button>
    <a href="/trades"
       class="text-xs px-4 py-2 rounded-lg font-medium transition-all"
       style="background: var(--accent-green-dim); color: var(--accent-green); border: 1px solid var(--accent-green)30;">
      View Recommendations
    </a>
  </div>
</div>

<!-- Positions + Log — stack on mobile, side-by-side on lg -->
<div class="grid gap-4 lg:grid-cols-2">

  <!-- Active positions -->
  <div class="rounded-xl card-border" style="background: var(--bg-card);">
    <div class="px-4 py-3 flex items-center justify-between" style="border-bottom: 1px solid var(--border);">
      <span class="text-sm font-semibold" style="color: var(--text-primary);">Active Positions</span>
      <span class="text-xs font-mono px-2 py-0.5 rounded"
            style="background: var(--accent-green-dim); color: var(--accent-green);">
        {activeTrades.length} open
      </span>
    </div>
    <div class="p-3 flex flex-col gap-2">
      {#each activeTrades as trade}
        <div class="p-3 rounded-lg" style="background: var(--bg-surface); border: 1px solid var(--border);">
          <div class="flex items-start justify-between gap-2 mb-2">
            <p class="text-xs leading-snug flex-1" style="color: var(--text-primary);">{trade.question}</p>
            <span class="text-xs font-mono font-bold flex-shrink-0"
                  style="color: {trade.action === 'YES' ? 'var(--accent-green)' : 'var(--accent-red)'};">
              {trade.action}
            </span>
          </div>
          <div class="flex items-center gap-3 text-xs flex-wrap" style="color: var(--text-muted);">
            <span>Entry <span class="font-mono" style="color: var(--text-secondary);">{trade.entryPrice.toFixed(2)}</span></span>
            <span>Now <span class="font-mono" style="color: var(--text-secondary);">{trade.currentPrice.toFixed(2)}</span></span>
            <span class="font-mono font-bold" style="color: {trade.pnl >= 0 ? 'var(--accent-green)' : 'var(--accent-red)'};">
              {trade.pnl >= 0 ? '+' : ''}${trade.pnl.toFixed(2)}
            </span>
          </div>
        </div>
      {/each}
      {#if activeTrades.length === 0}
        <div class="py-8 text-center text-xs" style="color: var(--text-muted);">No open positions</div>
      {/if}
    </div>
  </div>

  <!-- Activity log -->
  <div class="rounded-xl card-border" style="background: var(--bg-card);">
    <div class="px-4 py-3 flex items-center justify-between" style="border-bottom: 1px solid var(--border);">
      <span class="text-sm font-semibold" style="color: var(--text-primary);">Activity Log</span>
      <span class="text-xs" class:pulse={autoMode}
            style="color: {autoMode ? 'var(--accent-green)' : 'var(--text-muted)'};">
        {autoMode ? '● live' : '○ idle'}
      </span>
    </div>
    <!-- Capped height with scroll on all sizes -->
    <div class="p-3 font-mono text-xs overflow-y-auto flex flex-col gap-1.5"
         style="max-height: 280px;">
      {#each logs as log}
        <div class="flex gap-2">
          <span class="flex-shrink-0" style="color: var(--text-muted);">{log.time}</span>
          <span class="flex-shrink-0" style="color: {logColor(log.type)};">{logPrefix(log.type)}</span>
          <span class="break-words min-w-0" style="color: var(--text-secondary);">{log.msg}</span>
        </div>
      {/each}
    </div>
  </div>
</div>
