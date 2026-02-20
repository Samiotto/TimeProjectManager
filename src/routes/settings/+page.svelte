<div class="page-eyebrow">Preferences</div>
<div class="page-title">Settings</div>
<div class="page-desc">Configure Chrono to work the way you do.</div>

<div class="settings-group" style="max-width: 520px;">
  <div class="settings-group-title">General</div>

  <div class="settings-row">
    <div>
      <div class="settings-label">Theme</div>
      <div class="settings-desc">Appearance of the application</div>
    </div>
    <button class="btn btn-ghost" style="padding: 5px 14px; font-size: 9px;">Dark ▾</button>
  </div>

  <div class="settings-row">
    <div>
      <div class="settings-label">Clock Format</div>
      <div class="settings-desc">12-hour or 24-hour display</div>
    </div>
    <button class="btn btn-ghost" style="padding: 5px 14px; font-size: 9px;">12h ▾</button>
  </div>

  <div class="settings-row">
    <div>
      <div class="settings-label">Week Starts On</div>
      <div class="settings-desc">First day shown in calendar views</div>
    </div>
    <button class="btn btn-ghost" style="padding: 5px 14px; font-size: 9px;">Sunday ▾</button>
  </div>
</div>

<div class="settings-group" style="max-width: 520px;">
  <div class="settings-group-title">Sessions</div>

  <div class="settings-row">
    <div>
      <div class="settings-label">Auto-stop on idle</div>
      <div class="settings-desc">Stop session after inactivity</div>
    </div>
    <button class="toggle" aria-label="Toggle auto-stop on idle"></button>
  </div>

  <div class="settings-row">
    <div>
      <div class="settings-label">Confirm before stopping</div>
      <div class="settings-desc">Show confirmation dialog on stop</div>
    </div>
    <button class="toggle on" aria-label="Toggle confirm before stopping"></button>
  </div>
</div>

<div class="settings-group" style="max-width: 520px;">
  <div class="settings-group-title">Database</div>

  <div class="settings-row">
    <div>
      <div class="settings-label">Connection</div>
      <div class="settings-desc db-status">● Not connected</div>
    </div>
    <button class="btn btn-ghost" style="padding: 5px 14px; font-size: 9px;">Configure</button>
  </div>

  <div class="settings-row">
    <div>
      <div class="settings-label">Export Data</div>
      <div class="settings-desc">Download all sessions as CSV</div>
    </div>
    <button class="btn btn-ghost" style="padding: 5px 14px; font-size: 9px;">Export</button>
  </div>
</div>

<div class="settings-group" style="max-width: 520px;">
  <div class="settings-group-title">About</div>

  <div class="settings-row">
    <div>
      <div class="settings-label">Version</div>
    </div>
    <span class="version-label">0.1.0-dev</span>
  </div>
</div>

<style>
  .db-status {
    color: var(--text-lo);
  }

  .version-label {
    font-family: var(--font-mono);
    font-size: 11px;
    color: var(--text-lo);
  }
</style>
