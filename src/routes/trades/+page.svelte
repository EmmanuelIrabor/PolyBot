<script lang="ts">
  type Trade = {
    id: string;
    question: string;
    action: 'BUY YES' | 'BUY NO';
    confidence: number;
    entryPrice: number;
    targetPrice: number;
    suggestedAmount: number;
    expectedReturn: string;
    rationale: string;
    newsSignals: string[];
    risk: 'Low' | 'Medium' | 'High';
    category: string;
    selected: boolean;
  };

  let trades: Trade[] = [
    {
      id: '1', question: 'Will Bitcoin exceed $100k before June 2025?',
      action: 'BUY YES', confidence: 84, entryPrice: 0.62, targetPrice: 0.78,
      suggestedAmount: 200, expectedReturn: '+25.8%',
      rationale: 'Strong ETF inflows, halving cycle momentum, bullish on-chain metrics suggest continuation.',
      newsSignals: ['ETF inflows hit 3-month high', 'MicroStrategy buys more BTC', 'Halving cycle historically bullish'],
      risk: 'Medium', category: 'Crypto', selected: false,
    },
    {
      id: '2', question: 'Will the Fed cut rates in May 2025?',
      action: 'BUY NO', confidence: 71, entryPrice: 0.73, targetPrice: 0.88,
      suggestedAmount: 150, expectedReturn: '+20.5%',
      rationale: 'Inflation remains sticky at 3.2%. Fed dot plot signals "higher for longer" through H1.',
      newsSignals: ['CPI beat expectations at 3.2%', 'Powell: "patient approach warranted"', 'Jobs data remains robust'],
      risk: 'Low', category: 'Finance', selected: false,
    },
    {
      id: '3', question: 'Will Nvidia stock hit $1000 before end of 2025?',
      action: 'BUY YES', confidence: 67, entryPrice: 0.48, targetPrice: 0.65,
      suggestedAmount: 100, expectedReturn: '+35.4%',
      rationale: 'AI demand cycle intact. Blackwell ramp Q2. Datacenter orders accelerating significantly.',
      newsSignals: ['Blackwell chip demand exceeds supply', 'Microsoft doubles GPU order', 'H100 backlog extends to Q3'],
      risk: 'Medium', category: 'Stocks', selected: false,
    },
  ];

  $: totalAllocated = trades.filter(t => t.selected).reduce((sum, t) => sum + t.suggestedAmount, 0);
  $: selectedCount = trades.filter(t => t.selected).length;

  function toggleTrade(id: string) {
    trades = trades.map(t => t.id === id ? { ...t, selected: !t.selected } : t);
  }
  function selectAll() { trades = trades.map(t => ({ ...t, selected: true })); }
  function clearAll()  { trades = trades.map(t => ({ ...t, selected: false })); }

  function riskColor(risk: string) {
    if (risk === 'Low')    return 'var(--accent-green)';
    if (risk === 'Medium') return 'var(--accent-yellow)';
    return 'var(--accent-red)';
  }
  function actionColor(action: string) {
    return action === 'BUY YES' ? 'var(--accent-green)' : 'var(--accent-red)';
  }
  function actionBg(action: string) {
    return action === 'BUY YES' ? 'var(--accent-green-dim)' : 'var(--accent-red-dim)';
  }
</script>

<svelte:head><title>Trades · PolyBot</title></svelte:head>

<!-- Header + action bar -->
<div class="mb-4 flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between">
  <!-- Selection toolbar -->
  <div class="flex items-center gap-2 flex-wrap">
    <button onclick={selectAll}
            class="text-xs px-3 py-1.5 rounded-lg transition-all"
            style="background: var(--bg-card); color: var(--text-secondary); border: 1px solid var(--border);">
      Select All
    </button>
    {#if selectedCount > 0}
      <button onclick={clearAll}
              class="text-xs px-3 py-1.5 rounded-lg transition-all"
              style="background: var(--bg-card); color: var(--text-muted); border: 1px solid var(--border);">
        Clear
      </button>
    {/if}
  </div>

  <!-- Execute CTA — full width on mobile when trades selected -->
  {#if selectedCount > 0}
    <div class="flex items-center justify-between gap-3 px-4 py-2.5 rounded-xl w-full sm:w-auto"
         style="background: var(--bg-card); border: 1px solid var(--accent-green)40;">
      <span class="text-sm" style="color: var(--text-secondary);">
        {selectedCount} trade{selectedCount > 1 ? 's' : ''}
        <span class="font-mono ml-1" style="color: var(--accent-green);">${totalAllocated}</span>
      </span>
      <a href="/autobot"
         class="text-xs px-4 py-1.5 rounded-lg font-semibold transition-all"
         style="background: var(--accent-green); color: var(--bg-base);">
        Execute →
      </a>
    </div>
  {/if}
</div>

<!-- Trade cards -->
<div class="flex flex-col gap-4">
  {#each trades as trade}
    <div class="rounded-xl card-border overflow-hidden transition-all"
         style="background: var(--bg-card); {trade.selected ? 'border-color: var(--accent-green); box-shadow: 0 0 20px #00e5a012;' : ''}">

      <!-- Card header — tap to select -->
      <button
        onclick={() => toggleTrade(trade.id)}
        class="w-full text-left p-4 flex items-start gap-3"
      >
        <!-- Checkbox -->
        <span class="mt-0.5 w-5 h-5 rounded flex items-center justify-center flex-shrink-0 transition-all"
              style="
                background: {trade.selected ? 'var(--accent-green)' : 'transparent'};
                border: 2px solid {trade.selected ? 'var(--accent-green)' : 'var(--border-bright)'};
              ">
          {#if trade.selected}
            <span class="text-xs font-bold" style="color: var(--bg-base);">✓</span>
          {/if}
        </span>

        <!-- Content -->
        <div class="flex-1 min-w-0">
          <!-- Badges -->
          <div class="flex flex-wrap gap-1.5 mb-1.5">
            <span class="text-xs font-mono font-bold px-2 py-0.5 rounded"
                  style="background: {actionBg(trade.action)}; color: {actionColor(trade.action)};">
              {trade.action}
            </span>
            <span class="text-xs px-2 py-0.5 rounded"
                  style="color: {riskColor(trade.risk)}; background: {riskColor(trade.risk)}18;">
              {trade.risk} Risk
            </span>
            <span class="text-xs px-2 py-0.5 rounded"
                  style="background: var(--bg-hover); color: var(--text-muted);">
              {trade.category}
            </span>
          </div>
          <p class="text-sm font-medium leading-snug pr-2" style="color: var(--text-primary);">
            {trade.question}
          </p>
        </div>

        <!-- Confidence -->
        <div class="text-right flex-shrink-0">
          <div class="text-lg font-mono font-bold leading-none" style="color: var(--accent-green);">{trade.confidence}%</div>
          <div class="text-xs mt-0.5" style="color: var(--text-muted);">conf.</div>
        </div>
      </button>

      <!-- Expandable details (always visible on desktop, always shown) -->
      <div class="px-4 pb-4 border-t" style="border-color: var(--border);">
        <!-- Price metrics — 3 col on all sizes -->
        <div class="grid grid-cols-3 gap-2 mt-3 mb-3">
          {#each [['Entry', trade.entryPrice.toFixed(2)], ['Target', trade.targetPrice.toFixed(2)], ['Return', trade.expectedReturn]] as [label, val]}
            <div class="p-2 rounded-lg text-center" style="background: var(--bg-surface); border: 1px solid var(--border);">
              <div class="text-xs font-mono font-semibold"
                   style="color: {label === 'Return' ? 'var(--accent-green)' : 'var(--text-primary)'};">{val}</div>
              <div class="text-xs mt-0.5" style="color: var(--text-muted);">{label}</div>
            </div>
          {/each}
        </div>

        <!-- Rationale -->
        <p class="text-xs leading-relaxed mb-3" style="color: var(--text-secondary);">
          {trade.rationale}
        </p>

        <!-- News signals — wrap gracefully -->
        <div class="flex flex-wrap gap-1.5 mb-3">
          {#each trade.newsSignals as signal}
            <span class="text-xs px-2 py-0.5 rounded-full"
                  style="background: var(--bg-hover); color: var(--text-muted); border: 1px solid var(--border);">
              📰 {signal}
            </span>
          {/each}
        </div>

        <!-- Amount + select hint -->
        <div class="flex items-center justify-between">
          <span class="text-xs" style="color: var(--text-muted);">
            Suggested: <span class="font-mono font-bold" style="color: var(--accent-yellow);">${trade.suggestedAmount}</span>
          </span>
          <span class="text-xs" style="color: {trade.selected ? 'var(--accent-green)' : 'var(--text-muted)'};">
            {trade.selected ? '✓ Selected' : 'Tap to select'}
          </span>
        </div>
      </div>
    </div>
  {/each}
</div>
