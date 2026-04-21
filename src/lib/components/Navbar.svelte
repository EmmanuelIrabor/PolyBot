<script lang="ts">
  import { page } from '$app/stores';

  export let botActive = false;

  let mobileOpen = false;

  const nav = [
    { href: '/',          label: 'Markets',  icon: '◈', desc: 'Live predictions' },
    { href: '/trades',    label: 'Trades',   icon: '⟁', desc: 'AI recommendations' },
    { href: '/autobot',   label: 'AutoBot',  icon: '⬡', desc: 'Auto-execute' },
    { href: '/settings',  label: 'Settings', icon: '⚙', desc: 'Config & API keys' },
  ];

  function closeMobile() {
    mobileOpen = false;
  }

  $: activePath = $page.url.pathname;
</script>

<!-- ─────────────────────────────────────────────
     DESKTOP SIDEBAR (hidden on mobile)
───────────────────────────────────────────── -->
<aside
  class="hidden md:flex flex-col fixed left-0 top-0 h-screen z-50 w-16 lg:w-56 transition-all duration-300"
  style="background: var(--bg-surface); border-right: 1px solid var(--border);"
>
  <!-- Logo -->
  <div class="h-14 flex items-center px-4 gap-3 flex-shrink-0" style="border-bottom: 1px solid var(--border);">
    <div class="w-8 h-8 rounded-lg flex items-center justify-center text-sm font-bold flex-shrink-0"
         style="background: var(--accent-green); color: var(--bg-base);">
      P
    </div>
    <div class="hidden lg:block overflow-hidden">
      <div class="font-semibold text-sm leading-none" style="color: var(--text-primary);">PolyBot</div>
      <div class="text-xs mt-0.5" style="color: var(--text-muted);">Trading AI</div>
    </div>
  </div>

  <!-- Nav links -->
  <nav class="flex-1 py-4 flex flex-col gap-1 px-2 overflow-y-auto">
    {#each nav as item}
      {@const active = activePath === item.href}
      <a
        href={item.href}
        class="group flex items-center gap-3 px-3 py-2.5 rounded-lg text-sm font-medium transition-all duration-150 relative"
        style="
          color: {active ? 'var(--accent-green)' : 'var(--text-secondary)'};
          background: {active ? 'var(--accent-green-dim)' : 'transparent'};
        "
      >
        <!-- Active indicator bar -->
        {#if active}
          <span
            class="absolute left-0 top-1/2 -translate-y-1/2 w-0.5 h-5 rounded-r"
            style="background: var(--accent-green);"
          ></span>
        {/if}

        <span class="text-base w-5 text-center flex-shrink-0 transition-transform duration-150"
              class:scale-110={active}>
          {item.icon}
        </span>

        <span class="hidden lg:block leading-none">{item.label}</span>

        {#if active}
          <span class="hidden lg:block ml-auto w-1.5 h-1.5 rounded-full pulse"
                style="background: var(--accent-green);"></span>
        {/if}

        <!-- Tooltip for icon-only mode -->
        <span
          class="lg:hidden absolute left-14 px-2 py-1 rounded text-xs font-medium pointer-events-none
                 opacity-0 group-hover:opacity-100 transition-opacity whitespace-nowrap z-50"
          style="background: var(--bg-card); color: var(--text-primary); border: 1px solid var(--border); box-shadow: 0 4px 12px #00000060;"
        >
          {item.label}
        </span>
      </a>
    {/each}
  </nav>

  <!-- Bot status pill -->
  <div class="mx-2 mb-4 p-2.5 rounded-lg flex-shrink-0"
       style="background: var(--bg-card); border: 1px solid var(--border);">
    <div class="flex items-center gap-2">
      <span
        class="w-2 h-2 rounded-full flex-shrink-0"
        class:pulse={botActive}
        style="background: {botActive ? 'var(--accent-green)' : 'var(--text-muted)'};"
      ></span>
      <span class="hidden lg:block text-xs truncate" style="color: var(--text-secondary);">
        {botActive ? 'Bot Active' : 'Bot Idle'}
      </span>
    </div>
  </div>
</aside>

<!-- ─────────────────────────────────────────────
     MOBILE TOP BAR
───────────────────────────────────────────── -->
<header
  class="md:hidden fixed top-0 left-0 right-0 z-50 h-14 flex items-center justify-between px-4"
  style="background: var(--bg-surface); border-bottom: 1px solid var(--border);"
>
  <!-- Logo -->
  <div class="flex items-center gap-2.5">
    <div class="w-7 h-7 rounded-md flex items-center justify-center text-sm font-bold"
         style="background: var(--accent-green); color: var(--bg-base);">
      P
    </div>
    <span class="font-semibold text-sm" style="color: var(--text-primary);">PolyBot</span>
  </div>

  <!-- Right side: balance + menu -->
  <div class="flex items-center gap-3">
    <span class="text-xs font-mono" style="color: var(--text-secondary);">
      <span style="color: var(--accent-green);">$2,450</span>
    </span>
    <button
      onclick={() => mobileOpen = true}
      class="w-8 h-8 flex flex-col items-center justify-center gap-1.5 rounded-lg transition-all"
      style="background: var(--bg-card); border: 1px solid var(--border);"
      aria-label="Open menu"
    >
      <span class="w-4 h-px rounded" style="background: var(--text-secondary);"></span>
      <span class="w-4 h-px rounded" style="background: var(--text-secondary);"></span>
      <span class="w-3 h-px rounded" style="background: var(--text-secondary);"></span>
    </button>
  </div>
</header>

<!-- ─────────────────────────────────────────────
     MOBILE DRAWER OVERLAY
───────────────────────────────────────────── -->
{#if mobileOpen}
  <!-- Backdrop -->
  <div
    class="md:hidden fixed inset-0 z-[60]"
    style="background: #00000080; backdrop-filter: blur(2px);"
    onclick={closeMobile}
    role="presentation"
  ></div>

  <!-- Drawer panel -->
  <div
    class="md:hidden fixed top-0 right-0 bottom-0 z-[70] w-64 flex flex-col"
    style="background: var(--bg-surface); border-left: 1px solid var(--border);"
  >
    <!-- Drawer header -->
    <div class="h-14 flex items-center justify-between px-4" style="border-bottom: 1px solid var(--border);">
      <span class="font-semibold text-sm" style="color: var(--text-primary);">Navigation</span>
      <button
        onclick={closeMobile}
        class="w-7 h-7 flex items-center justify-center rounded-lg text-lg transition-all"
        style="color: var(--text-secondary); background: var(--bg-card); border: 1px solid var(--border);"
        aria-label="Close menu"
      >
        ×
      </button>
    </div>

    <!-- Drawer nav -->
    <nav class="flex-1 p-3 flex flex-col gap-1 overflow-y-auto">
      {#each nav as item}
        {@const active = activePath === item.href}
        <a
          href={item.href}
          onclick={closeMobile}
          class="flex items-center gap-3 px-3 py-3 rounded-xl transition-all"
          style="
            color: {active ? 'var(--accent-green)' : 'var(--text-secondary)'};
            background: {active ? 'var(--accent-green-dim)' : 'transparent'};
            border: 1px solid {active ? 'var(--accent-green)30' : 'transparent'};
          "
        >
          <span class="text-lg w-6 text-center flex-shrink-0">{item.icon}</span>
          <div class="flex-1 min-w-0">
            <div class="text-sm font-medium leading-none">{item.label}</div>
            <div class="text-xs mt-0.5 truncate" style="color: var(--text-muted);">{item.desc}</div>
          </div>
          {#if active}
            <span class="w-1.5 h-1.5 rounded-full pulse flex-shrink-0"
                  style="background: var(--accent-green);"></span>
          {/if}
        </a>
      {/each}
    </nav>

    <!-- Bot status in drawer -->
    <div class="p-3 m-3 rounded-xl" style="background: var(--bg-card); border: 1px solid var(--border);">
      <div class="flex items-center gap-2 mb-1">
        <span class="w-2 h-2 rounded-full flex-shrink-0"
              class:pulse={botActive}
              style="background: {botActive ? 'var(--accent-green)' : 'var(--text-muted)'};">
        </span>
        <span class="text-xs font-medium" style="color: var(--text-secondary);">
          {botActive ? 'Bot Active' : 'Bot Idle'}
        </span>
      </div>
      <div class="text-xs" style="color: var(--text-muted);">Polymarket · Claude AI · NewsAPI</div>
    </div>
  </div>
{/if}

<!-- ─────────────────────────────────────────────
     MOBILE BOTTOM NAV TAB BAR
───────────────────────────────────────────── -->
<nav
  class="md:hidden fixed bottom-0 left-0 right-0 z-50 flex items-stretch"
  style="background: var(--bg-surface); border-top: 1px solid var(--border); padding-bottom: env(safe-area-inset-bottom);"
>
  {#each nav as item}
    {@const active = activePath === item.href}
    <a
      href={item.href}
      class="flex-1 flex flex-col items-center justify-center py-2 gap-0.5 transition-all relative"
      style="color: {active ? 'var(--accent-green)' : 'var(--text-muted)'};"
    >
      {#if active}
        <span
          class="absolute top-0 left-1/2 -translate-x-1/2 w-8 h-0.5 rounded-b"
          style="background: var(--accent-green);"
        ></span>
      {/if}
      <span class="text-base leading-none">{item.icon}</span>
      <span class="text-xs leading-none" style="font-size: 10px;">{item.label}</span>
    </a>
  {/each}
</nav>
