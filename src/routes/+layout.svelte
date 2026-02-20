<script lang="ts">
  import "../app.css";
  import TabRail from "$lib/components/TabRail.svelte";
  import { page } from "$app/stores";

  let { children } = $props();

  let pageKey = $derived($page.url.pathname);
</script>

<div class="app-shell">
  <TabRail />
  <main class="content-area">
    {#key pageKey}
      <div class="page-transition">
        {@render children()}
      </div>
    {/key}
  </main>
</div>

<style>
  .app-shell {
    display: flex;
    height: 100vh;
    overflow: hidden;
    background: var(--bg);
  }

  .content-area {
    flex: 1;
    overflow: hidden;
    position: relative;
  }

  .page-transition {
    height: 100%;
    overflow-y: auto;
    padding: 36px 40px;
    animation: page-enter 0.32s ease both;
    scrollbar-width: thin;
    scrollbar-color: var(--border) transparent;
  }

  @keyframes page-enter {
    from {
      opacity: 0;
      transform: translateX(16px);
    }
    to {
      opacity: 1;
      transform: translateX(0);
    }
  }
</style>
