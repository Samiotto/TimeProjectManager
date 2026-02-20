<div class="page-eyebrow">Current Session</div>
<div class="page-title">Active Project</div>
<div class="page-desc">No active session. Select a project and start tracking your time.</div>

<div class="timer-display idle">--:--:--</div>
<div class="timer-status">
  <span class="dot dot-idle"></span>
  No session running
</div>

<div class="btn-row">
  <button class="btn btn-primary">
    <svg width="12" height="12" viewBox="0 0 24 24" fill="currentColor">
      <polygon points="5 3 19 12 5 21 5 3"/>
    </svg>
    Start Session
  </button>
  <button class="btn btn-ghost">Select Project</button>
</div>

<div class="card-grid" style="max-width: 540px;">
  <div class="card">
    <div class="card-label">Today</div>
    <div class="card-value">—</div>
    <div class="card-sub">hrs this session</div>
  </div>
  <div class="card">
    <div class="card-label">This Week</div>
    <div class="card-value">—</div>
    <div class="card-sub">hrs total</div>
  </div>
  <div class="card">
    <div class="card-label">All Time</div>
    <div class="card-value">—</div>
    <div class="card-sub">hrs logged</div>
  </div>
</div>

<div class="section-header" style="max-width: 540px;">
  <span class="section-label">Session Notes</span>
  <span class="section-line"></span>
  <button class="btn btn-ghost" style="padding: 5px 12px; font-size: 9px;">+ Add Note</button>
</div>

<div class="empty-state">Start a session to add notes.</div>

<style>
  .btn-row {
    display: flex;
    gap: 10px;
    margin-bottom: 40px;
  }
</style>
