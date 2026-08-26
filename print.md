---
title: "HIST 141 – Complete Course Materials"
layout: default
---

<style>
  #print-controls {
    background: #f5f5f5;
    border: 1px solid #ddd;
    border-radius: 6px;
    padding: 1.2em 1.5em;
    margin-bottom: 2em;
    display: flex;
    align-items: center;
    gap: 1.5em;
    flex-wrap: wrap;
  }
  #print-btn {
    background: #2a7ae2;
    color: white;
    border: none;
    padding: 0.6em 1.4em;
    font-size: 1em;
    border-radius: 4px;
    cursor: pointer;
    white-space: nowrap;
  }
  #print-btn:hover { background: #1756a9; }
  #loading { color: #666; font-style: italic; margin: 0; }
  .combined-section { margin-top: 3em; padding-top: 2em; border-top: 2px solid #ccc; }
  .combined-section:first-child { border-top: none; margin-top: 0; padding-top: 0; }
  @media print {
    #print-controls { display: none !important; }
    .combined-section { page-break-before: always; }
    .combined-section:first-child { page-break-before: avoid; }
    /* suppress nav links rendered by Jekyll theme */
    header, footer, nav { display: none !important; }
  }
</style>

<div id="print-controls">
  <button id="print-btn" onclick="window.print()">&#128424; Print / Save as PDF</button>
  <p id="loading">Loading all course materials&hellip;</p>
</div>

<div id="combined-content"></div>

<script>
// Add or remove pages here as index.md is split into smaller files.
const pages = [
  { url: 'index.html',       label: 'Syllabus' },
  { url: 'assignments.html', label: 'Grades & Assignments' },
  { url: 'policies.html',    label: 'Course & University Policies' },
  { url: 'Schedule.html',    label: 'Schedule' },
  { url: 'QforCs.html',      label: 'Questions for Consideration' }
];

// Try common Jekyll theme content selectors; fall back to cleaned body.
function extractContent(doc) {
  const selectors = [
    'article.post-content',
    '.post-content',
    'main .wrapper',
    '.page-content .wrapper',
    'main',
    'article',
    '.content',
    '.wrapper'
  ];
  for (const sel of selectors) {
    const el = doc.querySelector(sel);
    if (el) return el.cloneNode(true);
  }
  // Last resort: remove chrome elements from body clone
  const body = doc.body.cloneNode(true);
  ['header', 'nav', 'footer'].forEach(tag =>
    body.querySelectorAll(tag).forEach(el => el.remove())
  );
  return body;
}

async function loadAllPages() {
  const container = document.getElementById('combined-content');
  const loading   = document.getElementById('loading');

  for (const page of pages) {
    try {
      const res  = await fetch(page.url);
      const html = await res.text();
      const doc  = new DOMParser().parseFromString(html, 'text/html');

      // Strip the print-controls block so it doesn't appear in combined view
      doc.querySelectorAll('#print-controls, #combined-content').forEach(el => el.remove());

      const section = document.createElement('div');
      section.className = 'combined-section';
      section.appendChild(extractContent(doc));
      container.appendChild(section);
    } catch (e) {
      const err = document.createElement('p');
      err.style.color = 'red';
      err.textContent = 'Could not load ' + page.label + '. Make sure the site is served (not opened as a local file).';
      container.appendChild(err);
    }
  }

  loading.textContent = 'All materials loaded — click Print / Save as PDF above.';
}

loadAllPages();
</script>
