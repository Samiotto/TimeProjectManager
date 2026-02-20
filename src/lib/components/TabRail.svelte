<script lang="ts">
  import { page } from "$app/stores";
  import { goto } from "$app/navigation";

  interface Tab {
    id: string;
    label: string;
    route: string;
    icon: string;
  }

  const tabs: Tab[] = [
    {
      id: "active-project",
      label: "Active Project",
      route: "/",
      icon: `<circle cx="12" cy="12" r="10"/><polyline points="12 6 12 12 16 14"/>`,
    },
    {
      id: "projects",
      label: "Projects",
      route: "/projects",
      icon: `<path d="M3 7a2 2 0 0 1 2-2h3l2 2h9a2 2 0 0 1 2 2v8a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2z"/>`,
    },
    {
      id: "calendar",
      label: "Calendar",
      route: "/calendar",
      icon: `<rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/>`,
    },
    {
      id: "tasks",
      label: "Tasks",
      route: "/tasks",
      icon: `<rect x="3" y="3" width="8" height="8" rx="1"/><rect x="13" y="3" width="8" height="8" rx="1"/><rect x="3" y="13" width="8" height="8" rx="1"/><rect x="13" y="13" width="8" height="8" rx="1"/>`,
    },
    {
      id: "settings",
      label: "Settings",
      route: "/settings",
      icon: `<circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.65 1.65 0 0 0 .33 1.82l.06.06a2 2 0 0 1-2.83 2.83l-.06-.06a1.65 1.65 0 0 0-1.82-.33 1.65 1.65 0 0 0-1 1.51V21a2 2 0 0 1-4 0v-.09A1.65 1.65 0 0 0 9 19.4a1.65 1.65 0 0 0-1.82.33l-.06.06a2 2 0 0 1-2.83-2.83l.06-.06A1.65 1.65 0 0 0 4.68 15a1.65 1.65 0 0 0-1.51-1H3a2 2 0 0 1 0-4h.09A1.65 1.65 0 0 0 4.6 9a1.65 1.65 0 0 0-.33-1.82l-.06-.06a2 2 0 0 1 2.83-2.83l.06.06A1.65 1.65 0 0 0 9 4.68a1.65 1.65 0 0 0 1-1.51V3a2 2 0 0 1 4 0v.09a1.65 1.65 0 0 0 1 1.51 1.65 1.65 0 0 0 1.82-.33l.06-.06a2 2 0 0 1 2.83 2.83l-.06.06A1.65 1.65 0 0 0 19.4 9a1.65 1.65 0 0 0 1.51 1H21a2 2 0 0 1 0 4h-.09a1.65 1.65 0 0 0-1.51 1z"/>`,
    },
  ];

  const SETTINGS_HEIGHT = 60;
  const ACTIVE_HEIGHT = 260;
  const IDLE_WIDTH = 52;
  const ACTIVE_WIDTH = 220;

  const mainTabs = tabs.filter((t) => t.id !== "settings");
  const settingsTab = tabs.find((t) => t.id === "settings")!;

  let railHeight = $state(0);

  let activeTab = $derived.by(() => {
    const pathname = $page.url.pathname;
    const found = tabs.find((t) =>
      t.route === "/" ? pathname === "/" : pathname.startsWith(t.route),
    );
    return found?.id ?? "active-project";
  });

  let idleTabHeight = $derived.by(() => {
    const isSettingsActive = activeTab === "settings";
    const availableForMain = railHeight - SETTINGS_HEIGHT;

    if (isSettingsActive) {
      const count = mainTabs.length;
      return count > 0 ? Math.floor(availableForMain / count) : 0;
    } else {
      const idleCount = mainTabs.length - 1;
      const availableForIdle = availableForMain - ACTIVE_HEIGHT;
      return idleCount > 0 ? Math.floor(availableForIdle / idleCount) : 0;
    }
  });

  function handleTabClick(tab: Tab) {
    goto(tab.route);
  }

  function isActive(tabId: string): boolean {
    return activeTab === tabId;
  }
</script>

<nav
  class="tab-rail"
  bind:clientHeight={railHeight}
>
  {#each mainTabs as tab (tab.id)}
    {@const active = isActive(tab.id)}
    <button
      class="tab"
      class:active
      style="width: {active ? ACTIVE_WIDTH : IDLE_WIDTH}px; height: {active ? ACTIVE_HEIGHT : idleTabHeight}px;"
      onclick={() => handleTabClick(tab)}
    >
      {#if active}
        <span class="active-indicator"></span>
        <div class="tab-header">
          <svg
            width="18"
            height="18"
            viewBox="0 0 24 24"
            fill="none"
            stroke="currentColor"
            stroke-width="1.8"
            stroke-linecap="round"
            stroke-linejoin="round"
          >
            {@html tab.icon}
          </svg>
          <span class="tab-title">{tab.label}</span>
        </div>
      {:else}
        <span class="tab-label-rotated">{tab.label}</span>
      {/if}
    </button>
  {/each}

  <button
    class="tab settings-tab"
    class:active={isActive(settingsTab.id)}
    style="width: {isActive(settingsTab.id) ? ACTIVE_WIDTH : IDLE_WIDTH}px; height: {SETTINGS_HEIGHT}px; margin-top: auto;"
    onclick={() => handleTabClick(settingsTab)}
  >
    {#if isActive(settingsTab.id)}
      <span class="active-indicator"></span>
      <div class="tab-header">
        <svg
          width="18"
          height="18"
          viewBox="0 0 24 24"
          fill="none"
          stroke="currentColor"
          stroke-width="1.8"
          stroke-linecap="round"
          stroke-linejoin="round"
        >
          {@html settingsTab.icon}
        </svg>
        <span class="tab-title">{settingsTab.label}</span>
      </div>
    {:else}
      <span class="tab-label-rotated">{settingsTab.label}</span>
    {/if}
  </button>
</nav>

<style>
  .tab-rail {
    display: flex;
    flex-direction: column;
    width: 220px;
    height: 100vh;
    background: var(--tab-idle);
    border-right: 1px solid var(--border);
    overflow: hidden;
    flex-shrink: 0;
  }

  .tab {
    position: relative;
    border: none;
    cursor: pointer;
    overflow: hidden;
    display: flex;
    align-items: center;
    justify-content: center;
    background: var(--tab-idle);
    color: var(--text-mid);
    transition: width 0.38s cubic-bezier(0.4, 0, 0.2, 1);
    flex-shrink: 0;
    padding: 0;
  }

  .tab.active {
    background: var(--tab-active);
    color: var(--text-hi);
    align-items: flex-start;
    justify-content: flex-start;
  }

  .active-indicator {
    position: absolute;
    left: 0;
    top: 0;
    bottom: 0;
    width: 3px;
    background: var(--amber);
    transform-origin: bottom;
    animation: reveal-indicator 0.32s ease forwards;
  }

  @keyframes reveal-indicator {
    from {
      transform: scaleY(0);
    }
    to {
      transform: scaleY(1);
    }
  }

  .tab-header {
    display: flex;
    align-items: center;
    gap: 10px;
    padding: 16px 16px 0 20px;
    color: var(--amber);
  }

  .tab-title {
    font-family: "Playfair Display", serif;
    font-weight: 600;
    font-size: 14px;
    color: var(--text-hi);
    white-space: nowrap;
  }

  .tab-label-rotated {
    font-family: "DM Mono", monospace;
    font-size: 10px;
    font-weight: 400;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--text-lo);
    writing-mode: vertical-rl;
    transform: rotate(180deg);
    white-space: nowrap;
  }

  .tab::before {
    content: "";
    position: absolute;
    top: 0;
    left: 0;
    width: 8px;
    height: 8px;
    background: var(--tab-active);
    clip-path: polygon(0 0, 100% 0, 0 100%);
    opacity: 0;
  }

  .tab.active::before {
    opacity: 1;
  }
</style>
