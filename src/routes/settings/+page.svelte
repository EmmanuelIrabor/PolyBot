<script lang="ts">
  let saved = false;

  let config = {
    polymarketKey: '', claudeKey: '', newsApiKey: '', openaiKey: '',
    minConfidence: 65, maxPerTrade: 250, dailyLimit: 1000,
    riskLevel: 'Medium', notifyOnTrade: true, webhookUrl: '',
  };

  function save() {
    saved = true;
    setTimeout(() => (saved = false), 2000);
  }
</script>

<svelte:head><title>Settings · PolyBot</title></svelte:head>

<!-- Settings grid: single col mobile, 2 col lg -->
<div class="grid gap-4 lg:grid-cols-2 max-w-4xl">

  <!-- API Keys -->
  <div class="rounded-xl card-border p-4" style="background: var(--bg-card);">
    <h2 class="text-sm font-semibold mb-4 flex items-center gap-2" style="color: var(--text-primary);">
      <span style="color: var(--accent-green);">◈</span> API Keys
    </h2>
    <div class="flex flex-col gap-3">
      {#each [
        ['Polymarket API Key',   'polymarketKey', 'pk_live_...'],
        ['Claude (Anthropic)',   'claudeKey',     'sk-ant-...'],
        ['NewsAPI Key',          'newsApiKey',    'newsapi_...'],
        ['OpenAI Key (optional)','openaiKey',     'sk-...'],
      ] as [label, field, placeholder]}
        <div>
          <label class="text-xs block mb-1" style="color: var(--text-secondary);">{label}</label>
          <input
            type="password"
            bind:value={config[field as keyof typeof config]}
            {placeholder}
            class="w-full px-3 py-2 rounded-lg text-xs font-mono outline-none transition-all"
            style="background: var(--bg-surface); border: 1px solid var(--border); color: var(--text-primary);"
          />
        </div>
      {/each}
    </div>
  </div>

  <!-- Risk Parameters -->
  <div class="rounded-xl card-border p-4" style="background: var(--bg-card);">
    <h2 class="text-sm font-semibold mb-4 flex items-center gap-2" style="color: var(--text-primary);">
      <span style="color: var(--accent-yellow);">⟁</span> Risk Parameters
    </h2>
    <div class="flex flex-col gap-4">
      {#each [
        { label: 'Min Confidence', key: 'minConfidence', min: 50, max: 95, step: 1,   unit: '%',  color: 'var(--accent-green)' },
        { label: 'Max per Trade',  key: 'maxPerTrade',   min: 25, max: 1000,step: 25,  unit: '$',  color: 'var(--accent-yellow)', prefix: true },
        { label: 'Daily Limit',    key: 'dailyLimit',    min: 100,max: 5000,step: 100, unit: '$',  color: 'var(--accent-blue)',   prefix: true },
      ] as item}
        <div>
          <label class="text-xs flex justify-between mb-1.5" style="color: var(--text-secondary);">
            <span>{item.label}</span>
            <span class="font-mono font-semibold" style="color: {item.color};">
              {item.prefix ? '$' : ''}{config[item.key as keyof typeof config]}{!item.prefix ? item.unit : ''}
            </span>
          </label>
          <input type="range" min={item.min} max={item.max} step={item.step}
                 bind:value={config[item.key as keyof typeof config]}
                 class="w-full h-1.5 rounded appearance-none cursor-pointer"
                 style="accent-color: {item.color};" />
        </div>
      {/each}

      <!-- Risk level selector -->
      <div>
        <label class="text-xs block mb-2" style="color: var(--text-secondary);">Risk Level</label>
        <div class="grid grid-cols-3 gap-2">
          {#each [['Low', 'var(--accent-green)'], ['Medium', 'var(--accent-yellow)'], ['High', 'var(--accent-red)']] as [level, color]}
            <button
              onclick={() => config.riskLevel = level}
              class="py-2 rounded-lg text-xs font-medium transition-all"
              style="
                background: {config.riskLevel === level ? color + '18' : 'var(--bg-surface)'};
                color: {config.riskLevel === level ? color : 'var(--text-muted)'};
                border: 1px solid {config.riskLevel === level ? color : 'var(--border)'};
              ">{level}</button>
          {/each}
        </div>
      </div>
    </div>
  </div>

  <!-- Notifications -->
  <div class="rounded-xl card-border p-4" style="background: var(--bg-card);">
    <h2 class="text-sm font-semibold mb-4 flex items-center gap-2" style="color: var(--text-primary);">
      <span style="color: var(--accent-blue);">⬡</span> Notifications
    </h2>
    <div class="flex flex-col gap-4">
      <div class="flex items-center justify-between gap-4">
        <div>
          <div class="text-xs font-medium" style="color: var(--text-secondary);">Notify on execution</div>
          <div class="text-xs mt-0.5" style="color: var(--text-muted);">Alert when a trade is placed</div>
        </div>
        <button
          onclick={() => config.notifyOnTrade = !config.notifyOnTrade}
          class="relative w-10 h-5 rounded-full flex-shrink-0 transition-colors"
          style="background: {config.notifyOnTrade ? 'var(--accent-green)' : 'var(--border-bright)'};"
          aria-label="Toggle notification">
          <span class="absolute top-0.5 w-4 h-4 bg-white rounded-full shadow transition-all"
                style="left: {config.notifyOnTrade ? '22px' : '2px'};"></span>
        </button>
      </div>
      <div>
        <label class="text-xs block mb-1.5" style="color: var(--text-secondary);">Webhook URL</label>
        <input type="url" bind:value={config.webhookUrl}
               placeholder="https://hooks.slack.com/..."
               class="w-full px-3 py-2 rounded-lg text-xs font-mono outline-none"
               style="background: var(--bg-surface); border: 1px solid var(--border); color: var(--text-primary);" />
        <p class="text-xs mt-1" style="color: var(--text-muted);">Works with Discord, Slack, or any webhook endpoint</p>
      </div>
    </div>
  </div>

  <!-- Deployment -->
  <div class="rounded-xl card-border p-4" style="background: var(--bg-card);">
    <h2 class="text-sm font-semibold mb-4 flex items-center gap-2" style="color: var(--text-primary);">
      <span style="color: var(--accent-purple);">⚙</span> Railway Deployment
    </h2>
    <div class="flex flex-col gap-3">
      <div class="p-3 rounded-lg" style="background: var(--bg-surface); border: 1px solid var(--border);">
        <p class="text-xs leading-relaxed" style="color: var(--text-muted);">
          Deploy the bot runner and scheduler to Railway. Set env vars from API keys above in your Railway project dashboard.
        </p>
      </div>
      <div class="flex flex-wrap items-center gap-2">
        <span class="text-xs font-mono px-2 py-1 rounded"
              style="background: var(--accent-green-dim); color: var(--accent-green);">● Connected</span>
        <span class="text-xs" style="color: var(--text-muted);">polybot-prod · us-east</span>
      </div>
      <div class="p-3 rounded-lg font-mono text-xs" style="background: var(--bg-base); border: 1px solid var(--border); color: var(--text-secondary);">
        <div style="color: var(--text-muted);"># Deploy via CLI</div>
        <div>railway login</div>
        <div>railway up</div>
      </div>
    </div>
  </div>
</div>

<!-- Save -->
<div class="mt-5 flex items-center gap-3">
  <button onclick={save}
          class="px-6 py-2.5 rounded-lg text-sm font-semibold transition-all"
          style="
            background: {saved ? 'var(--accent-green)' : 'var(--accent-green-dim)'};
            color: {saved ? 'var(--bg-base)' : 'var(--accent-green)'};
            border: 1px solid var(--accent-green)40;
          ">
    {saved ? '✓ Saved!' : 'Save Settings'}
  </button>
  {#if saved}
    <span class="text-xs" style="color: var(--text-muted);">Changes saved successfully</span>
  {/if}
</div>
