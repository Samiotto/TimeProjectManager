<script lang="ts">
  const today = new Date();
  const todayDate = today.getDate();
  const year = today.getFullYear();
  const month = today.getMonth();

  const monthName = today.toLocaleString("default", { month: "long" });
  const heading = `${monthName} ${year}`;

  const firstDayOfWeek = new Date(year, month, 1).getDay(); // 0 = Sun
  const daysInMonth = new Date(year, month + 1, 0).getDate();

  interface Cell {
    day: number | null;
    isToday: boolean;
  }

  const cells: Cell[] = [];
  for (let i = 0; i < firstDayOfWeek; i++) {
    cells.push({ day: null, isToday: false });
  }
  for (let d = 1; d <= daysInMonth; d++) {
    cells.push({ day: d, isToday: d === todayDate });
  }
  while (cells.length % 7 !== 0) {
    cells.push({ day: null, isToday: false });
  }

  const DOW = ["Sun", "Mon", "Tue", "Wed", "Thu", "Fri", "Sat"];
</script>

<div class="page-eyebrow">Timetable</div>
<div class="page-title">{heading}</div>
<div class="page-desc">A record of where your time has gone. Switch between month, week, and day views.</div>

<div class="cal-nav">
  <button class="btn btn-ghost" style="padding: 5px 12px; font-size: 9px;">‹ Prev</button>
  <button class="btn btn-ghost" style="padding: 5px 12px; font-size: 9px;">Today</button>
  <button class="btn btn-ghost" style="padding: 5px 12px; font-size: 9px;">Next ›</button>
  <div class="view-toggle">
    <button class="btn btn-ghost active-view" style="padding: 5px 12px; font-size: 9px;">Month</button>
    <button class="btn btn-ghost" style="padding: 5px 12px; font-size: 9px;">Week</button>
    <button class="btn btn-ghost" style="padding: 5px 12px; font-size: 9px;">Day</button>
  </div>
</div>

<div class="cal-grid">
  {#each DOW as dow}
    <div class="cal-day-header">{dow}</div>
  {/each}
  {#each cells as cell}
    {#if cell.day === null}
      <div class="cal-day empty"></div>
    {:else}
      <div class="cal-day" class:today={cell.isToday}>
        <div class="cal-day-num">{cell.day}</div>
      </div>
    {/if}
  {/each}
</div>

<style>
  .cal-nav {
    display: flex;
    gap: 8px;
    align-items: center;
    margin-bottom: 24px;
  }

  .view-toggle {
    display: flex;
    gap: 4px;
    margin-left: auto;
  }

  .active-view {
    background: var(--surface2);
    color: var(--text-hi);
  }
</style>
