# AGENTS.md — Guidance for AI Coding Agents & Automated Contributors

> **Target Repository**: `211nj-help`  
> **Primary File**: [`index.html`](file:///c:/Users/Ryan/Desktop/211nj-help/index.html)  
> **Target Audience**: Ryan, fiancée (autism/anxiety), child (Asperger's / ASD), and residents of Sicklerville (08081) / Winslow Township / Camden County, NJ.  
> **Last Verified Core Data**: October 2026.

---

## 1. Core Mission & High-Stakes Domain Context

This repository is not a generic static mock-up or demo. It is an **active, high-utility civic navigation system and emergency survival guide** tailored for a specific, neurodivergent household facing complex public benefits rules in New Jersey.

### Human Stakes
* **Financial Risk**: Benefit rules in New Jersey (and federally) are punitive regarding reporting deadlines, household composition, and asset caps. Giving outdated information about Supplemental Security Income (SSI) asset thresholds or marriage rules can cause someone to permanently lose disability checks, housing vouchers, or Medicaid health coverage.
* **Cognitive & Anxiety Risk**: The household includes individuals with autism and severe phone/social anxiety. Do not introduce vague instructions, complicated interactive widgets, or bloated copy. Always provide direct online self-service links, text-message options (`898-211`), and exact verbatim scripts for interacting with caseworkers.
* **Emergency Resilience**: In a power outage, storm, or utility shutoff, this page may be accessed offline on a dying mobile phone or printed onto physical paper. It must render immediately, cleanly, and without depending on network connections.

---

## 2. Architectural Guardrails & Invariants

Whenever an AI agent inspects, modifies, refactors, or extends this project, it **MUST adhere strictly to the following invariants**:

### Invariant 1: Single-File Delivery Model
* The core application is packaged entirely within [`index.html`](file:///c:/Users/Ryan/Desktop/211nj-help/index.html).
* Do not split CSS or JavaScript into external `.css` or `.js` files unless there is an explicit request and consensus to migrate to a bundled architecture.
* The single-file model guarantees that the file can be emailed, transferred over USB, saved to a phone's filesystem, or opened via `file:///` without missing asset errors or broken paths.

### Invariant 2: Zero External Runtime Dependencies
* **No external scripts, stylesheets, fonts, or analytics.**
* Do not link to Google Fonts (e.g. `fonts.googleapis.com`), external icon sets (FontAwesome), Bootstrap, Tailwind CDN, React/Vue, or jQuery.
* All fonts rely on robust system fallback stacks:
  ```css
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  ```
  with display titles gracefully targeting `'Google Sans', 'Product Sans', 'Segoe UI', Roboto, Arial, sans-serif`.
* Icons are rendered using native Unicode characters and HTML entities (e.g., `&#127915;` 🎫, `&#9888;` ⚠, `&#10003;` ✓, `&uarr;` ↑).

### Invariant 3: Navigation Dual-Mode Synchronization
The Table of Contents navigation must remain 100% in sync between the desktop sticky sidebar and the mobile swipeable pill-tab bar:
* When adding or renaming any section (`<h2 class="section-title" id="...">`), you **MUST** update the `<nav class="toc"> <ol>` list inside [`index.html`](file:///c:/Users/Ryan/Desktop/211nj-help/index.html#L549-L579).
* Internal anchor IDs must match exactly (e.g., `href="#cash"` matches `id="cash"`).
* Sub-items (such as museum passes) must use the class `toc-sub` (`<li class="toc-sub"><a href="#library-passes">...</a></li>`).
* The scroll-spy JavaScript dynamically discovers sections via:
  ```javascript
  var tocLinks = Array.prototype.slice.call(document.querySelectorAll('.toc a[href^="#"]'));
  ```
  Ensure that every link starts with `#` and has a corresponding DOM element with that matching ID.

### Invariant 4: Print Stylesheet Preservation (`@media print`)
Physical printouts and PDF exports are first-class delivery channels:
* Never remove or break the `@media print` stylesheet block.
* Ensure all interactive-only components (`.toolbar-btn`, `.qs-reset`) remain hidden in print (`display: none;`).
* Ensure `.panel`, `.quickstart`, `.toc`, and `.alert-box` retain `break-inside: avoid;` to prevent awkward splits across printed pages.
* Ensure high contrast is maintained (dark text on white backgrounds, minimal ink consumption).

### Invariant 5: LocalStorage Checklist Contract
The Quickstart checklist uses a lightweight, robust client-side persistence pattern:
* Storage key format: `qs211-{data-qs}` (e.g., `qs211-1`, `qs211-2`, ..., `qs211-8`).
* If you modify or add checklist items:
  1. Assign a unique integer to `data-qs`.
  2. Maintain the container classes `.quickstart li.done` for strikethrough and opacity adjustments.
  3. Ensure the event delegation handles both direct checkbox clicks and full-row click targets without double-toggling.
  4. Ensure `refresh()` updates `#qs-count`, `#qs-pct`, and `#qs-progress-fill` correctly.

### Invariant 6: The Absolute Candor Convention (`.unverified`)
In social safety net documentation, presenting an educated guess as a verified fact is dangerous.
* **Verified Data**: Clearly mark dates using `<p class="verified-note">&#10003;&nbsp; Figures last verified <strong>[Month Year]</strong> ...</p>`.
* **Unconfirmed or Volatile Data**: If an agent updates or adds a program detail, policy threshold, or local service that has not been confirmed with primary 2026/2027 state documentation, the agent **MUST** wrap the note in:
  ```html
  <p class="unverified"><strong>&#9888; Not fully confirmed:</strong> [Exact explanation of what could not be independently verified and what the user should ask].</p>
  ```
* Never delete existing `.unverified` warnings unless you have verified the facts with primary sources and updated the underlying copy.

---

## 3. Design System & CSS Conventions

The portal employs a clean, Google-inspired visual hierarchy defined by custom CSS variables in [`index.html`](file:///c:/Users/Ryan/Desktop/211nj-help/index.html#L8-L24):

### Color Tokens
```css
:root {
  --bg: #ffffff;
  --surface: #f8f9fa;
  --header-bg: #eaecf0;
  --border: #dadce0;
  --border-light: #e8eaed;
  --text: #202124;
  --muted: #5f6368;
  --link: #1a73e8;
  --link-visited: #1967d2;
  --accent: #4285f4;
  
  /* Google 4-Color Palette */
  --g-blue: #4285f4;   --g-blue-bg: #e8f0fe;   --g-blue-ink: #1967d2;
  --g-red: #ea4335;    --g-red-bg: #fce8e6;    --g-red-ink: #c5221f;
  --g-yellow: #fbbc04; --g-yellow-bg: #fef7e0; --g-yellow-ink: #b06000;
  --g-green: #34a853;  --g-green-bg: #e6f4ea;  --g-green-ink: #188038;
}
```

### Component Patterns

#### 1. Standard Content Panel
```html
<div class="panel">
  <div class="panel-head">Program Title <span class="tag">Program Acronym / Category</span></div>
  <div class="panel-body">
    <ul>
      <li>Key requirement or actionable benefit</li>
      <li>Eligibility rule or documentation note</li>
    </ul>
    <div class="contact-row">
      <span class="name">Agency Name</span>
      <span class="num"><a href="tel:1-800-XXX-XXXX">1-800-XXX-XXXX</a></span>
      <span class="desc">Hours / Details</span>
    </div>
  </div>
</div>
```

#### 2. Notice Callouts
* **Actionable Pointer Box (Passes / Opportunities)**: `<div class="pass-callout">...</div>` (light blue background, solid blue left border).
* **High-Priority Alert (Urgent Warning / Marriage Risk)**: `<div class="alert-box">...</div>` (light red background, solid red left border).
* **Unconfirmed Information**: `<p class="unverified">...</p>` (light yellow background, solid yellow left border).

#### 3. Income Tables
```html
<table class="income-table">
  <tr><th>Household Size</th><th>Max Monthly Income</th></tr>
  <tr><td>1 person</td><td>$X,XXX</td></tr>
  <tr><td>2 people</td><td>$X,XXX</td></tr>
</table>
```

---

## 4. JavaScript Runtime Rules

The JavaScript in [`index.html`](file:///c:/Users/Ryan/Desktop/211nj-help/index.html#L2350-L2484) is written in clean, browser-native JavaScript:
1. **No Modern Transpiler Required**: Keep code compatible with modern and legacy mobile browsers. Avoid cutting-edge ES syntax that would fail on older Android or iOS devices without a build step.
2. **Scroll-Spy Mechanics**:
   - Uses `IntersectionObserver` with `{ rootMargin: '-5% 0px -60% 0px' }` to track whichever section occupies the reader's primary focus.
   - Updates the `.active` class on navigation links.
   - Mobile: Centers the active pill in the horizontal bar using `keepActiveVisible()`.
   - Desktop: Keeps the active sidebar item in view without yanking the main window.
3. **Print Event Listeners**:
   - `beforeprint` event clears active TOC highlights to ensure pristine monochrome printing.

---

## 5. Verification Checklist for Agents

Before completing any task or committing changes to this repository, run through this quality assurance checklist:

- [ ] **HTML Integrity**: All tags are properly closed; no broken markup.
- [ ] **ID Uniqueness**: All DOM `id` attributes are unique across the entire document.
- [ ] **TOC Parity**: All `<a href="#...">` links in `<nav class="toc">` have a corresponding `<... id="...">` in `<main class="content">`.
- [ ] **Checklist Persistence**: If checklist items were touched, verify that `data-qs` indices are unique, the count reflects `X of N complete`, and `localStorage` keys function properly.
- [ ] **Contact Rows & Telephony Links**: All telephone numbers use `<a href="tel:...">` format without spaces or letters in the `tel:` scheme.
- [ ] **Candor Flags**: Any unverified program rule is flagged with `<p class="unverified">`.
- [ ] **Print Verification**: No interactive controls appear when simulated under print media.
- [ ] **Mobile Responsiveness**: Confirm that viewport meta tags and media queries (`@media (max-width: 820px)`) remain intact.

---

## 6. Associated Documentation Links

When consulting or updating repository documentation, cross-reference these companion guides:
* [README.md](file:///c:/Users/Ryan/Desktop/211nj-help/README.md) — Main repository documentation and project overview.
* [docs/ARCHITECTURE.md](file:///c:/Users/Ryan/Desktop/211nj-help/docs/ARCHITECTURE.md) — Technical and structural specifications.
* [docs/PROGRAMS_AND_BENEFITS.md](file:///c:/Users/Ryan/Desktop/211nj-help/docs/PROGRAMS_AND_BENEFITS.md) — Comprehensive benefits program reference.
* [docs/LOCAL_RESOURCES_CAMDEN_COUNTY.md](file:///c:/Users/Ryan/Desktop/211nj-help/docs/LOCAL_RESOURCES_CAMDEN_COUNTY.md) — Geographic and facility directory for Camden County / Sicklerville.
* [docs/MAINTENANCE_AND_VERIFICATION.md](file:///c:/Users/Ryan/Desktop/211nj-help/docs/MAINTENANCE_AND_VERIFICATION.md) — Verification schedule and audit protocols.
* [docs/DEPLOYMENT_AND_OFFLINE_USAGE.md](file:///c:/Users/Ryan/Desktop/211nj-help/docs/DEPLOYMENT_AND_OFFLINE_USAGE.md) — Hosting, mobile install, and offline readiness guide.
