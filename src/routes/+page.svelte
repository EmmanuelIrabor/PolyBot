<script lang="ts">
  type Market = {
    id: string;
    question: string;
    category: string;
    endDate: string;
    yesPrice: number;
    noPrice: number;
    volume: string;
    liquidity: string;
    sentiment: 'bullish' | 'bearish' | 'neutral';
    aiScore: number;
    trending: boolean;
  };

  const markets: Market[] = [
    { id: '1', question: 'Will Bitcoin exceed $100k before June 2025?', category: 'Crypto', endDate: '2025-06-01', yesPrice: 0.62, noPrice: 0.38, volume: '$4.2M', liquidity: '$890K', sentiment: 'bullish', aiScore: 84, trending: true },
    { id: '2', question: 'Will the Fed cut rates in May 2025?', category: 'Finance', endDate: '2025-05-07', yesPrice: 0.27, noPrice: 0.73, volume: '$2.1M', liquidity: '$430K', sentiment: 'bearish', aiScore: 71, trending: true },
    { id: '3', question: 'Will SpaceX complete Starship orbital flight by Q2 2025?', category: 'Tech', endDate: '2025-06-30', yesPrice: 0.55, noPrice: 0.45, volume: '$1.8M', liquidity: '$310K', sentiment: 'neutral', aiScore: 60, trending: false },
    { id: '4', question: 'Will Ethereum ETF net inflows exceed $1B in April 2025?', category: 'Crypto', endDate: '2025-04-30', yesPrice: 0.41, noPrice: 0.59, volume: '$3.3M', liquidity: '$720K', sentiment: 'bearish', aiScore: 55, trending: false },
    { id: '5', question: 'Will Nvidia stock hit $1000 before end of 2025?', category: 'Stocks', endDate: '2025-12-31', yesPrice: 0.48, noPrice: 0.52, volume: '$900K', liquidity: '$180K', sentiment: 'neutral', aiScore: 67, trending: true },
  ];

  let filter = 'All';
  const categories = ['All', 'Crypto', 'Finance', 'Tech', 'Stocks'];

  $: filtered = filter === 'All' ? markets : markets.filter(m => m.category === filter);

  function scoreColor(score: number) {
    if (score >= 75) return 'var(--accent-green)';
    if (score >= 50) return 'var(--accent-yellow)';
    return 'var(--accent-red)';
  }

  function sentimentBadge(s: string) {
    if (s === 'bullish') return { color: 'var(--accent-green)', bg: 'var(--accent-green-dim)', label: '▲ Bullish' };
    if (s === 'bearish') return { color: 'var(--accent-red)', bg: 'var(--accent-red-dim)', label: '▼ Bearish' };
    return { color: 'var(--text-secondary)', bg: 'var(--bg-hover)', label: '◆ Neutral' };
  }

  function daysLeft(dateStr: string) {
    const diff = new Date(dateStr).getTime() - Date.now();
    return Math.max(0, Math.ceil(diff / 86400000));
  }
</script>

<svelte:head><title>Markets · PolyBot</title></svelte:head>

<!-- Page header -->
<div class="mb-5 flex flex-col gap-3 sm:flex-row sm:items-center sm:justify-between mt-5">
  <!-- Stats strip — scrollable on mobile -->
  <div class="flex gap-2 overflow-x-auto pb-1 sm:pb-0 sm:order-2 flex-shrink-0">
    {#each [['Markets', markets.length], ['Trending', markets.filter(m => m.trending).length], ['Top Score', '84']] as [label, val]}
      <div class="px-3 py-1.5 rounded-lg text-center flex-shrink-0"
           style="background: var(--bg-card); border: 1px solid var(--border);">
        <div class="text-sm font-mono font-bold" style="color: var(--accent-green);">{val}</div>
        <div class="text-xs" style="color: var(--text-muted);">{label}</div>
      </div>
    {/each}
  </div>
</div>

<!-- Category filter — horizontally scrollable on mobile -->
<div class="flex gap-2 mb-4 overflow-x-auto pb-1" style="-webkit-overflow-scrolling: touch;">
  {#each categories as cat}
    <button
      onclick={() => filter = cat}
      class="px-3 py-1.5 rounded-full text-xs font-medium transition-all flex-shrink-0"
      style="
        background: {filter === cat ? 'var(--accent-green)' : 'var(--bg-card)'};
        color: {filter === cat ? 'var(--bg-base)' : 'var(--text-secondary)'};
        border: 1px solid {filter === cat ? 'var(--accent-green)' : 'var(--border)'};
      ">
      {cat}
    </button>
  {/each}
</div>

<!-- Markets list -->
<div class="flex flex-col gap-3">
  {#each filtered as market}
    {@const badge = sentimentBadge(market.sentiment)}
    <div class="rounded-xl card-border transition-all cursor-pointer group"
         style="background: var(--bg-card);">

      <!-- Card body -->
      <div class="p-4">
        <!-- Top row: badge + question + score (mobile: stacked; desktop: side-by-side) -->
        <div class="flex items-start justify-between gap-3 mb-3">
          <div class="flex-1 min-w-0">
            <!-- Tags row -->
            <div class="flex flex-wrap items-center gap-1.5 mb-2">
              <span class="text-xs px-1.5 py-0.5 rounded font-medium"
                    style="background: var(--bg-hover); color: var(--text-secondary);">
                {market.category}
              </span>
              {#if market.trending}
                <span class="text-xs px-1.5 py-0.5 rounded font-mono"
                      style="background: var(--accent-yellow); color: var(--bg-base); font-size: 10px;">
                  HOT
                </span>
              {/if}
              <span class="text-xs px-1.5 py-0.5 rounded"
                    style="color: {badge.color}; background: {badge.bg};">
                {badge.label}
              </span>
            </div>
            <!-- Question -->
            <p class="text-sm font-medium leading-snug" style="color: var(--text-primary);">
              {market.question}
            </p>
          </div>

          <!-- AI Score bubble — always visible -->
          <div class="flex-shrink-0 text-center w-12">
            <div class="text-xl font-mono font-bold leading-none"
                 style="color: {scoreColor(market.aiScore)};">{market.aiScore}</div>
            <div class="text-xs mt-0.5" style="color: var(--text-muted); font-size: 10px;">AI</div>
          </div>
        </div>

        <!-- Probability bar -->
        <div class="flex items-center gap-2 mb-3">
          <span class="text-xs font-mono w-14 text-right flex-shrink-0" style="color: var(--accent-green);">
            YES {(market.yesPrice * 100).toFixed(0)}%
          </span>
          <div class="flex-1 prob-bar">
            <div class="prob-bar-fill" style="width: {market.yesPrice * 100}%; background: var(--accent-green);"></div>
          </div>
          <span class="text-xs font-mono w-14 flex-shrink-0" style="color: var(--accent-red);">
            NO {(market.noPrice * 100).toFixed(0)}%
          </span>
        </div>

        <!-- Meta row + CTA -->
        <div class="flex items-center justify-between gap-2 flex-wrap">
          <div class="flex flex-wrap gap-3 text-xs" style="color: var(--text-muted);">
            <span>Vol <span style="color: var(--text-secondary);">{market.volume}</span></span>
            <span>Liq <span style="color: var(--text-secondary);">{market.liquidity}</span></span>
            <span><span style="color: var(--text-secondary);">{daysLeft(market.endDate)}d</span> left</span>
          </div>
          <a href="/trades"
             class="text-xs px-3 py-1.5 rounded-lg font-medium transition-all"
             style="background: var(--accent-green-dim); color: var(--accent-green); border: 1px solid var(--accent-green)30;">
            Trade →
          </a>
        </div>
      </div>
    </div>
  {/each}
</div>
