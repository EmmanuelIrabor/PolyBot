<script lang="ts">
  import '../app.css';
  import Navbar from '$lib/components/Navbar.svelte';
  import { page } from '$app/stores';

  let botActive = false;

  const pageTitles: Record<string, { title: string; sub: string }> = {
    '/':          { title: 'Live Markets',  sub: 'Upcoming Polymarket predictions' },
    '/trades':    { title: 'AI Trades',     sub: 'Claude-analyzed recommendations' },
    '/autobot':   { title: 'AutoBot',       sub: 'Automated trade execution' },
    '/settings':  { title: 'Settings',      sub: 'Config & API keys' },
  };

  $: meta = pageTitles[$page.url.pathname] ?? { title: 'PolyBot', sub: '' };
</script>

<div class="min-h-screen" style="background: var(--bg-base);">
  <Navbar {botActive} />

  <!--
    Layout offsets:
    - md+: left sidebar is 64px (w-16) → ml-16
    - lg+: left sidebar is 224px (w-56) → ml-56
    - mobile: top bar = 56px (pt-14) + bottom nav = 56px (pb-14)
  -->
  <div class="md:ml-16 lg:ml-56 flex flex-col min-h-screen">

    <!-- Top bar (desktop only — mobile has its own header in Navbar) -->
    <header
      class="hidden md:flex h-14 items-center justify-between px-5 sticky top-0 z-40 flex-shrink-0"
      style="background: var(--bg-base); border-bottom: 1px solid var(--border);"
    >
      <!-- Page title breadcrumb -->
      <div>
        <h1 class="text-sm font-semibold leading-none" style="color: var(--text-primary);">{meta.title}</h1>
        <p class="text-xs mt-0.5" style="color: var(--text-muted);">{meta.sub}</p>
      </div>

      <!-- Right: status + balance + avatar -->
      <div class="flex items-center gap-3">
        <span class="text-xs font-mono px-2 py-1 rounded flex items-center gap-1.5"
              style="background: var(--bg-card); color: var(--accent-green); border: 1px solid var(--border);">
          <span class="w-1.5 h-1.5 rounded-full pulse" style="background: var(--accent-green);"></span>
          LIVE
        </span>
        <span class="text-xs font-mono hidden lg:block" style="color: var(--text-secondary);">
          Balance: <span style="color: var(--accent-green);">$2,450.00</span>
        </span>
        <div class="w-7 h-7 rounded-full flex items-center justify-center text-xs font-bold"
             style="background: var(--accent-blue); color: white;">
          U
        </div>
      </div>
    </header>

    <!-- Page content -->
    <!-- pt-14 for mobile topbar, pb-16 for mobile bottom nav, px padding -->
    <main class="flex-1 pt-14 pb-16 md:pt-0 md:pb-0 px-4 sm:px-5 md:px-6 py-4 md:py-6">
      <slot />
    </main>
  </div>
</div>
