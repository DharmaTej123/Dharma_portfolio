# Implementation Plan: Portfolio Enhancements

## Overview

All work is inline edits to the single static `index.html` (no build step, no framework, no package manager). The implementation follows the script's existing top-to-bottom shape — CONFIG/DATA → renderers → animations/observers → interaction handlers — and reuses the established `LOGOS`→`buildLogoTrack()` idiom for three new data-driven renderers (`renderCerts`, `renderSkills`, `renderProjects`).

The tasks are sequenced so data exists before renderers, renderers exist before they are wired ahead of the IntersectionObserver line and `bindCursor()`, and the static markup is only emptied after its renderer is proven. Structural edits (Publication section, section renumbering, social links, EmailJS refactor, visual identity, CSS cleanup) follow, then a final manual browser verification against the design checklist.

Optional property-based renderer tests (fast-check + jsdom, dev-only, run outside the shipped file) are included and marked with `*` so they never force a toolchain onto the build-free single-file site.

## Tasks

- [x] 1. Add the consolidated CONFIG / DATA block at the top of the `<script>`
  - Replace the current loose `EMAILJS_PUBLIC_KEY` / `EMAILJS_SERVICE_ID` / `EMAILJS_TEMPLATE_ID` constants (near line 1283) and the placeholder `PHOTO_URLS` with one clearly labeled `CONFIG / DATA` region
  - Add the `EMAILJS_CONFIG` object `{ publicKey, serviceId, templateId, toEmail }` using the existing values (`zc-29wzrNwAE9hF2q`, `service_l9ood5i`, `template_29hiige`, `sunkaradharmateja1729@gmail.com`)
  - Include the maintainer SECURITY NOTE comment: EmailJS public key is a publishable client key visible in page source; mitigation is the EmailJS dashboard domain allow-list (Allowed Origins); no private/API keys in this file
  - Add the `CERTS` array with all 9 entries (`name`, `issuer`, `url`, `img`) exactly per the design Data Models table, including the two PDF-backed entries (`github_copilot.pdf`, `Azure_Fundamentals.pdf`) and the AWS Partner filename with the literal space and `(1)`
  - Add the `SKILLS` structure: 5 groups (`AI & Agentic` 🤖, `Backend & Engineering` ⚙️, `Cloud` ☁️, `Databases` 🗄️, `Engineering Excellence` 🎯) transcribed verbatim from the current `#skills .skills-grid` markup, each with `{ icon, title, tags: [...] }`
  - Add the `PROJECTS` array in new-first order (DocuMind, LoopDesk, RepoSage, then the existing YouTube Channel Q&A Assistant, Inventory Management System, Legacy Code Modernization Agent) with the exact descriptions from Requirements 5.2/6.2/7.2 and the exact tag lists from the Data Models table; existing three retain current desc/tags
  - _Requirements: 3.1, 3.4, 5.1, 5.2, 5.3, 6.1, 6.2, 6.3, 7.1, 7.2, 7.3, 8.1, 8.4, 9.1, 9.4, 15.1, 15.2_

- [x] 2. Implement the certifications renderer and its supporting CSS
  - [x] 2.1 Implement `renderCerts()`
    - Read `#certs .certs-grid`, clear it, and for each `CERTS` entry create `<a class="cert-card" href="{url}" target="_blank" rel="noopener">` with an `aria-label` like `View credential: {name}`
    - When `img` is a non-empty string, render `<img class="cert-badge" src="{img}" alt="{name} badge">` with an `onerror` that hides the image and reveals the `.cert-icon` fallback glyph; when `img` is empty/missing, render the fallback glyph directly
    - Always render `.cert-name` (display name) and `.cert-issuer` (issuer · Verify ↗) so the card is meaningful without an image; keep the anchor and `href` intact on image failure
    - Skip malformed entries (missing `name` or `url`) instead of throwing
    - _Requirements: 3.2, 3.3, 3.5, 4.1, 4.2, 4.3, 4.4, 4.5_
  - [x] 2.2 Add supporting cert CSS
    - Extend `.cert-card` to work as a block anchor (`text-decoration:none; color:inherit; cursor:none;`) and add `.cert-badge { width:40px; height:40px; object-fit:contain; }`, preserving the existing `.cert-card` layout/hover so appearance is unchanged
    - _Requirements: 4.1, 2.3_
  - [ ]* 2.3 Write property test for certifications renderer
    - **Property 1: Certifications render one card per entry** — for any `CERTS` array, exactly one `.cert-card` per well-formed entry; adding one entry yields one more card
    - **Property 2: Each certification renders as a credential anchor to its URL** — rendered element is an `<a>` with `href === url`, `target="_blank"`, `rel="noopener"`
    - **Property 3: Missing-image cert stays clickable with fallback** — entry with empty/absent `img` shows the fallback glyph + name and remains a clickable anchor with `href === url`
    - fast-check + jsdom, dev-only, min 100 iterations; tag: `Feature: portfolio-enhancements, Property 1/2/3`
    - **Validates: Requirements 3.2, 3.3, 3.5, 4.2, 4.4, 4.5**

- [x] 3. Implement the skills renderer
  - [x] 3.1 Implement `renderSkills()`
    - Read `#skills .skills-grid`, clear it, and for each `SKILLS` group build a `.skill-group` containing `.skill-group-icon` (icon), `.skill-group-title` (title), and a `.skill-tags` wrapper with one `.skill-tag` span per label
    - Preserve existing markup/animation classes so appearance and reveal animation are unchanged
    - _Requirements: 9.2, 9.3, 9.4, 2.3_
  - [ ]* 3.2 Write property test for skills renderer
    - **Property 6: Skills render every group and every label** — exactly one `.skill-group` per group, each renders its icon and title, and exactly one `.skill-tag` per label
    - fast-check + jsdom, dev-only, min 100 iterations; tag: `Feature: portfolio-enhancements, Property 6`
    - **Validates: Requirements 9.2, 9.3, 9.4**

- [x] 4. Implement the projects renderer
  - [x] 4.1 Implement `renderProjects()`
    - Read `#projects .projects-grid`, clear it, and for each entry at index `i` build a `.project-card` with `.project-num` = `String(i+1).padStart(2,'0')` (`01`, `02`, …), `.project-name`, `.project-desc`, and a `.project-stack` wrapper with one `.stack-tag` span per tag, tags in order
    - Skip malformed entries (missing `name`); numbering is derived from index so it stays sequential regardless of count
    - _Requirements: 8.2, 8.3, 8.4, 8.5_
  - [ ]* 4.2 Write property tests for projects renderer
    - **Property 4: Projects render one sequentially numbered card per entry** — one `.project-card` per entry; card at index `i` shows `String(i+1).padStart(2,'0')`; numbers are `01..NN` with no gaps/repeats
    - **Property 5: Each project card contains its name, description, and ordered tags** — card text includes `name` and `desc`; exactly one `.stack-tag` per tag in order
    - fast-check + jsdom, dev-only, min 100 iterations; tag: `Feature: portfolio-enhancements, Property 4/5`
    - **Validates: Requirements 8.2, 8.3, 8.4, 8.5**

- [x] 5. Wire the renderer invocations into the load sequence
  - Call `renderCerts()`, `renderSkills()`, and `renderProjects()` in the CONFIG/renderers region (top of script, where `buildLogoTrack()` and `buildPhotoMarquee()` already run), so they execute BEFORE the generic reveal observer's `document.querySelectorAll('.exp-item,.skill-group,.cert-card,.project-card').forEach(el => obs.observe(el))` line (near line 1681) and BEFORE `bindCursor()` (near line 1656)
  - Confirm the per-card `transitionDelay` stagger loops (skill-group / cert-card / project-card) run after the observer wiring and therefore find the rendered cards
  - _Requirements: 3.2, 8.2, 9.2_

- [x] 6. Convert the Certifications, Skills, and Projects static markup to empty grid containers
  - Remove the hardcoded `.cert-card` divs inside `#certs .certs-grid` (currently 7 static cards), leaving the empty `.certs-grid` container
  - Remove the hardcoded `.skill-group` divs inside `#skills .skills-grid` (currently 5 static groups), leaving the empty `.skills-grid` container
  - Remove the hardcoded `.project-card` divs inside `#projects .projects-grid` (currently 3 static cards), leaving the empty `.projects-grid` container
  - Keep each section's `<section>`, `.section-inner`, `.section-label`, `.section-title`, and grid wrapper intact so layout/appearance is preserved and the renderers populate them
  - _Requirements: 2.2, 2.3, 3.2, 8.2, 9.2_

- [x] 7. Checkpoint - verify data-driven sections render
  - Ensure all tests pass, ask the user if questions arise.

- [x] 8. Add the new Publication section
  - [x] 8.1 Insert the Publication section markup
    - Add `<section id="publications">` after Projects and before Certifications, as static section `04` markup: `section-label` "04 — Publication", a `section-title`, `.pub-title` (Face Recognition with Liveness Detection using Computer Vision: Enhancing Security in Biometric Systems), `.pub-desc` (final-year research combining deep-learning facial feature extraction with real-time physiological-signal analysis to fortify biometric auth against spoofing/fraud)
    - Add a `.pub-link` `<a>` to `https://drive.google.com/file/d/1NISEe9m5vZHm8w9qIGDh5BAsLy7ILX3T/view` with `target="_blank" rel="noopener"`
    - Add the two images `FRLD_demo.jpg` and `Publication_certificate.jpg`, each with `onerror="this.style.display='none'"` so a failed image hides only itself without disrupting layout
    - _Requirements: 10.1, 10.2, 10.3, 10.4, 10.5, 10.6_
  - [x] 8.2 Add Publication CSS
    - Add `.pub-card`, `.pub-body`, `.pub-title`, `.pub-desc`, `.pub-link`, `.pub-media` rules reusing the palette (`--surface`, `--border`, `--accent`) and the existing `.edu-card`/`.project-card` idiom, keeping text legible on the dark background; make the two images independent flex/grid children so hiding one leaves siblings laid out
    - _Requirements: 10.5, 12.3, 2.3_

- [x] 9. Fix all section numbering to sequential document order
  - Update the static `.section-label` text so numbers run `01..10` in document order per the design's section table: 01 Experience, 02 Skills, 03 Projects, 04 Publication, 05 Certifications, 06 Education, 07 Journey, 08 Strengths, 09 Beyond Work, 10 Contact
  - This corrects the current disorder (Certifications is 04, Journey 07, and Contact is 06 despite appearing last) so there are no gaps or repeats and Contact (10) is greater than the section immediately before it (Beyond Work 09)
  - Add an HTML comment above the first section documenting the ordering contract so future inserts renumber consciously
  - _Requirements: 1.1, 1.2, 1.3, 10.6_

- [x] 10. Correct and de-duplicate the Contact social links
  - In the Contact section, set LinkedIn to `https://www.linkedin.com/in/dharma-teja-sunkara-0704b5226`, GitHub to `https://github.com/DharmaTej123`, and Credly to `https://www.credly.com/users/dharma-teja-sunkara/badges/credly`, each `target="_blank" rel="noopener"`
  - Keep the LeetCode link with an inline `TODO(maintainer)` placeholder comment marking the real profile URL as pending
  - Ensure each distinct social link is rendered only once
  - _Requirements: 11.1, 11.2, 11.3, 11.4, 11.5_

- [x] 11. Refactor EmailJS init and `sendEmail()` to use `EMAILJS_CONFIG`
  - Replace `emailjs.init(EMAILJS_PUBLIC_KEY)` with an init that uses `EMAILJS_CONFIG.publicKey`, guarded so it only initializes when the config is not empty/placeholder
  - Update `sendEmail()` to reference `EMAILJS_CONFIG.publicKey/serviceId/templateId/toEmail` in the `emailjs.send(...)` call, preserving all existing fields, validation, spinner, success panel, and toast behavior
  - Generalize the demo-mode guard: treat the config as unconfigured when `publicKey` is empty or matches a placeholder sentinel (e.g. `YOUR_PUBLIC_KEY`), and in that case run the demo-mode success/toast path without a live send; keep the live send + success/error toasts unchanged when configured
  - _Requirements: 14.1, 14.2, 14.3, 14.4, 15.1, 15.3_

- [ ]* 12. Write property test for the EmailJS demo-mode branch
  - **Property 7: Placeholder EmailJS config routes to demo mode** — for any config whose `publicKey` is empty or the placeholder sentinel, a valid submit takes the demo-mode branch and does NOT call live `emailjs.send`; for any non-placeholder `publicKey`, a valid submit routes to the live send path
  - Extract/expose the config-classification predicate so it can be exercised with a mocked `emailjs.send`; fast-check + jsdom, dev-only, min 100 iterations; tag: `Feature: portfolio-enhancements, Property 7`
  - **Validates: Requirements 15.3**

- [x] 13. Elevate Gen AI / Agentic AI / Data visual identity
  - Strengthen identity cues in prominent areas (hero badge/typewriter phrases centered on Agentic AI / RAG / multi-agent, consistent accent usage across Projects and the new Publication theming) using low-risk, appearance-preserving touches
  - Retain the dark theme, `--accent #ff8a00`, custom cursor, `#techCanvas` particle field, typewriter hero, and both marquees; keep all sections legible at the site's `--text`/`--accent` colors on the dark background; introduce no new heavy assets
  - _Requirements: 12.1, 12.2, 12.3, 13.1_

- [x] 14. CSS cleanup
  - Merge the two `body::before` rules into a single rule that preserves the intended layered radial/linear background PLUS the fine grid lines (moving the dot grid to `body::after` as already done), so no selector rule-set is duplicated and the rendered look is unchanged
  - Remove the commented-out `.journey-*` block entirely, keeping only the active `.journey-*` rules
  - Replace the undefined `var(--orange)` in `.btn-secondary:hover { border-color: ... }` with `var(--accent)` to preserve the orange hover using an existing token
  - _Requirements: 2.1, 2.3_

- [x] 15. Final checkpoint - manual browser verification against the design checklist
  - Open `index.html` directly (`file://`) and via a simple static server in a current desktop browser and verify:
    - Render counts: exactly 9 cert cards; exactly 6 project cards numbered `01`–`06` with DocuMind/LoopDesk/RepoSage first; Skills shows all groups and every tag
    - Section numbering reads `01…10` in document order; Contact is `10` and greater than Beyond Work `09`
    - Link targets: each of the 9 cert cards opens its exact credential URL in a new tab; LinkedIn/GitHub/Credly open the corrected URLs in new tabs and appear once; Publication link opens the Google Drive URL in a new tab; LeetCode retains its pending TODO comment in source
    - Keyboard access: tab through Certifications — each card focuses and activates with Enter
    - Image fallbacks: the two PDF-backed cert entries (GitHub Copilot, Azure AI Fundamentals) show the fallback glyph and stay clickable; a Publication image pointed at a bad path hides without breaking layout
    - Appearance/identity: dark theme, `--accent #ff8a00`, custom cursor, canvas particles, typewriter hero, and both marquees still work; converted sections look as before
    - Contact form: with real keys a valid submit sends and shows success and a forced failure shows the error toast; with placeholder/empty `publicKey` submit enters demo mode without a live send
  - Ensure everything renders with no build step and all dependencies load from external references; ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for a faster path; they are dev-only property tests (fast-check + jsdom) run outside the shipped file and do NOT add a build step or runtime dependency to `index.html`.
- Each task references specific requirements for traceability.
- Checkpoints (tasks 7 and 15) ensure incremental validation.
- Property tests validate the universal renderer/config-branch behavior; the manual checklist validates static content, links, numbering, CSS cleanup, visual identity, and the third-party EmailJS send.

## Task Dependency Graph

```json
{
  "waves": [
    { "id": 0, "tasks": ["1"] },
    { "id": 1, "tasks": ["2.1", "2.2", "3.1", "4.1", "11"] },
    { "id": 2, "tasks": ["2.3", "3.2", "4.2", "5", "12"] },
    { "id": 3, "tasks": ["6"] },
    { "id": 4, "tasks": ["8.1", "8.2", "9", "10", "13", "14"] }
  ]
}
```
