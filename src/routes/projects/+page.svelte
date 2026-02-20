<div class="page-eyebrow">All Work</div>
<div class="page-title">Projects</div>
<div class="page-desc">Select a project to view details or start a work session. Favorites always appear first.</div>

<div class="toolbar">
  <button class="btn btn-primary">+ New Project</button>
  <div class="sort-control">
    <span class="sort-label">Sort:</span>
    <button class="btn btn-ghost" style="padding: 5px 12px; font-size: 9px;">Last Opened</button>
  </div>
</div>

<div class="section-header">
  <span class="section-label">★ Favorites</span>
  <span class="section-line"></span>
</div>
<div class="project-list">
  <div class="empty-state" style="padding: 12px 16px;">No favorites yet.</div>
</div>

<div class="section-header">
  <span class="section-label">Projects</span>
  <span class="section-line"></span>
</div>
<div class="project-list">
  <div class="empty-state" style="padding: 12px 16px;">No projects yet. Create your first project above.</div>
</div>

<div class="section-header archived-header">
  <span class="section-label">▸ Archived</span>
  <span class="section-line"></span>
</div>

<style>
  .toolbar {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-bottom: 24px;
  }

  .sort-control {
    display: flex;
    align-items: center;
    gap: 8px;
    margin-left: auto;
  }

  .sort-label {
    font-family: var(--font-mono);
    font-size: 9px;
    letter-spacing: 0.12em;
    text-transform: uppercase;
    color: var(--text-lo);
  }

  .archived-header {
    cursor: pointer;
  }
</style>
