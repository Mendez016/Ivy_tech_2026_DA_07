# Financial Aid Portal — Plan

## Overview

Build a single-file, front-end-only web application (`index.html`) that helps first-generation students understand and act on their financial aid status. The app reads all data from the existing `case_data.js` file (which mirrors the CSV datasets) and requires no server, no build tools, and no framework — it runs by opening the HTML file in any browser.

+The app has two navigation states:

- **Public** (no student ID required): Four tabs are shown — **FAQs**, **Contacts**, **Glossary**, and **Log In**. All three content tabs are fully usable without a student ID, and FAQ answers carry inline glossary tooltips.
- **Authenticated** (valid student ID entered): The Log In tab is replaced by a **Sign Out** control, and two additional tabs are revealed — **My Status** and **Deadlines**. The app automatically switches to My Status after a successful login.

The visual tone is clean, calm, and reassuring — soft colors, plain-language labels, and clear status badges (green = complete, red/orange = action needed).

---

## Data Sources

All data is served from `window.CASE_DATA` defined in `case_data.js`:

| Key | Description |
|-----|-------------|
| `aid_status` | Per-student checklist of requirements, their status, deadlines, and how-to instructions |
| `notices` | Personal letters sent to each student |
| `deadline_calendar` | Upcoming financial aid dates and who they apply to |
| `aid_glossary` | 10 term/definition pairs for inline tooltips and a glossary section |
| `faqs` | 10 question/answer pairs |
| `contacts` | 4 office contact cards |

Student IDs present in the data: `S4001`, `S4002`, `S4003`.

---

## Sub-Tasks

---

### Sub-Task 1 — Project Scaffold and Shell Layout

**Status:** `[ ] pending`

**Intent:**  
Create the single `index.html` file with the full page structure, tab navigation skeleton, and a `<script src="case_data.js">` reference. This establishes the HTML skeleton and CSS design system (colors, typography, badge styles, card styles) that all subsequent sub-tasks will fill in.

**Expected Outcomes:**
- `index.html` exists in the data folder alongside `case_data.js`.
- The page loads in a browser without errors.
- On load, the tab bar shows four tabs: **FAQs**, **Contacts**, **Glossary**, and **Log In**. Log In is selected by default.
- After authentication, the tab bar shows: **FAQs**, **Contacts**, **Glossary**, **My Status**, **Deadlines**, and a **Sign Out** control. The Log In tab is no longer shown.
- The CSS design system is in place: soft background color, clean sans-serif font, status badge classes (`.badge-complete`, `.badge-outstanding`, `.badge-pending`), card styles, and responsive layout.

**Todo List:**
1. Create `index.html` with standard HTML5 boilerplate.
2. Add `<script src="case_data.js">` before the closing `</body>`.
3. Write the CSS design system inside a `<style>` block: color palette (soft blues/greens), typography, button, badge, card, and tab styles.
4. Write the public tab bar HTML with four tabs: FAQs, Contacts, Glossary, Log In.
5. Write the authenticated tab bar additions: My Status, Deadlines tabs and a Sign Out button — hidden until authentication.
6. Add empty `<section>` containers for each tab's content (FAQs, Contacts, Glossary, Log In, My Status, Deadlines) — these will be populated in later sub-tasks.
7. Write the tab-switching JS logic (show/hide sections on tab click).

**Relevant Context:**
- `case_data.js` defines `window.CASE_DATA` — all JS must guard against this being undefined.
- The four public tabs (FAQs, Contacts, Glossary, Log In) are always visible on load.
- The two authenticated tabs (My Status, Deadlines) and Sign Out must be hidden (`display:none`) until a valid student ID is confirmed.
- The Log In tab must be hidden after authentication; Sign Out must be hidden before authentication.

---

### Sub-Task 2 — Log In Tab and Authentication Logic

**Status:** `[ ] pending`

**Intent:**
Implement the Log In tab content (student ID form) and the logic that looks up the entered ID, toggles the tab bar between public and authenticated states, and stores the session. This is the gating mechanism for personalized My Status and Deadlines content.

**Expected Outcomes:**
- The Log In tab renders a centered form with a labeled student-ID input and a "View My Aid" button.
- Entering `S4001`, `S4002`, or `S4003` and clicking "View My Aid" hides the Log In tab, reveals the My Status and Deadlines tabs and Sign Out control, and switches the view to "My Status".
- Entering an unknown ID shows a plain-language inline error message ("We couldn't find that Student ID. Please check and try again.") within the Log In tab, without crashing.
- Clicking Sign Out clears `currentStudentId`, hides authenticated tabs, re-shows the Log In tab, and returns the tab selection to Log In.
- The currently loaded student ID is stored in a module-level JS variable (e.g., `currentStudentId`) used by all other tab renderers.

**Todo List:**
1. Write the Log In tab HTML: a `<label>`, text `<input>`, submit `<button>`, and an empty error message `<p>`.
2. Write a `findStudent(id)` function that searches `CASE_DATA.aid_status` for a matching `student_id` and returns `true`/`false`.
3. Wire the "View My Aid" button's click handler to call `findStudent`, then either transition to authenticated state or display the inline error.
4. Write `setAuthState(authenticated)` to toggle tab visibility: hide Log In tab + show My Status and Deadlines tabs when `true`; reverse when `false`.
5. Wire the Sign Out button to call `setAuthState(false)`, clear `currentStudentId`, and re-select the Log In tab.
6. Auto-switch to the "My Status" tab on successful login.

**Relevant Context:**
- Student IDs in `CASE_DATA.aid_status` are `S4001`, `S4002`, `S4003` — lookup should be case-insensitive and trim whitespace to be forgiving.
- Do not hard-code student IDs — always derive valid IDs from the data.
- The Log In tab is a regular tab panel, not a modal or separate page — it appears and disappears as part of the tab bar.

---

### Sub-Task 3 — My Status Tab

**Status:** `[ ] pending`

**Intent:**  
Render the student's personalized financial aid checklist from `aid_status`, clearly distinguishing complete vs. outstanding items, and surfacing actionable steps for anything that requires the student's attention. This is the most important screen in the app.

**Expected Outcomes:**
- All `aid_status` rows for the logged-in student are displayed as a checklist.
- Each row shows: item name, item type, a colored status badge, and deadline (if present).
- Outstanding/Pending items are expanded to show an "Action Required" card containing: what to do (`how_to_complete`), deadline, consequence of missing it (`if_missed`), and the responsible office.
- Complete items show a checkmark and are visually de-emphasized.
- The student's personal notices (from `CASE_DATA.notices`) appear below the checklist in a "Your Notices" section.
- Glossary terms found in the item names or action cards are wrapped in a tooltip span (see Sub-Task 6 for the tooltip implementation).

**Todo List:**
1. Write a `renderStatus(studentId)` function that filters `CASE_DATA.aid_status` by `student_id`.
2. For each item, generate a checklist row with the appropriate badge class based on `status` value.
3. For items with `status !== 'Complete'`, render an expanded action card below the row showing `how_to_complete`, `deadline`, `if_missed`, and `office`.
4. Write a `renderNotices(studentId)` function that filters `CASE_DATA.notices` by `student_id` and renders each notice as an expandable card (click subject to reveal body).
5. Call both render functions when the authenticated session is established and whenever the tab is re-activated.

**Relevant Context:**
- `aid_status` status values observed: `Complete`, `Outstanding`, `Pending`, `Offered`, `On hold` — the badge system must cover all of these.
- `how_to_complete`, `deadline`, and `if_missed` are empty strings for `Complete` items — handle gracefully.
- `notices` rows: `notice_id`, `student_id`, `date_sent`, `subject`, `body`.

---

### Sub-Task 4 — FAQs Tab

**Status:** `[ ] pending`

**Intent:**  
Render all 10 FAQ entries from `CASE_DATA.faqs` as an accessible accordion — always visible to any visitor (public tab), no student ID required.

**Expected Outcomes:**
- All 10 questions are listed on the FAQs tab.
- Clicking a question expands its answer; clicking again collapses it.
- Only one answer is open at a time (single-expand accordion).
- Glossary terms appearing in answers are wrapped in tooltip spans — visible to all users, no student ID required.
- The tab is accessible before any student ID is entered.

**Todo List:**
1. Write a `renderFAQs()` function that iterates `CASE_DATA.faqs` and generates accordion HTML.
2. Wire click handlers to toggle expanded state per item.
3. Apply glossary tooltip wrapping to FAQ answer text by calling `applyTooltips()` — this utility must be initialized at page load, not gated by authentication.
4. Call `renderFAQs()` once on page load (not gated by authentication).

**Relevant Context:**
- `faqs` rows: `question`, `answer`.
- The accordion should use CSS transitions for smooth expand/collapse.
- `applyTooltips()` is defined in Sub-Task 6 but must be callable at page load — ensure it is declared before `renderFAQs()` runs.

---

### Sub-Task 5 — Deadlines Tab

**Status:** `[ ] pending`

**Intent:**
Show the full `deadline_calendar` as a chronologically sorted list. Items whose `applies_to` field matches the authenticated student's context are visually highlighted to draw attention. This tab is only visible after a valid student ID is entered.

**Expected Outcomes:**
- All 8 deadline entries are displayed in date order.
- Each entry shows: date (formatted human-readably), event name, and who it applies to.
- Deadlines that are relevant to the authenticated student (based on their outstanding `aid_status` items) are highlighted with a distinct visual treatment.
- Past deadlines are visually de-emphasized (e.g., gray text).
- Tab is only visible after authentication (one of the two authenticated-only tabs, alongside My Status).

**Todo List:**
1. Write a `renderDeadlines(studentId)` function that sorts `CASE_DATA.deadline_calendar` by date.
2. Determine relevant deadlines: cross-reference `applies_to` text with the student's outstanding item types and statuses.
3. Render each deadline as a list item, applying a highlight class for relevant items and a muted class for past dates (compare against today's date).
4. Format dates from `YYYY-MM-DD` to a human-readable form (e.g., "December 15, 2026").

**Relevant Context:**
- `deadline_calendar` rows: `date`, `event`, `applies_to`.
- `applies_to` values include "All students", "Students selected for verification", etc. — use substring/keyword matching against the student's status items to flag relevance.

---

### Sub-Task 6 — Glossary Tab and Inline Tooltips

**Status:** `[ ] pending`

**Intent:**
Render all 10 glossary terms in a dedicated Glossary tab that is always publicly accessible (no student ID required), and implement an `applyTooltips()` utility that wraps glossary terms found in other tabs' content with inline tooltip spans. This helps first-generation students understand jargon in-context anywhere in the app.

**Expected Outcomes:**
- Glossary tab is public — visible on page load alongside FAQs, Contacts, and Log In.
- Glossary tab lists all 10 terms with their plain-language definitions.
- `renderGlossary()` is called once on page load (not gated by authentication).
- An `applyTooltips(htmlString)` utility function scans a string for glossary terms (case-insensitive, whole-word match) and wraps them in `<span class="glossary-term" data-def="...">term</span>`.
- Tooltips appear on hover (desktop) and tap (mobile) showing the definition in a small floating box.
- Tooltips are applied in: FAQ answers, Contacts text (all public), and status item names, action card text, notice body text (authenticated).

**Todo List:**
1. Write the `applyTooltips(htmlString)` utility early in the script block (before any render functions) so it is available at page load.
2. Write a `renderGlossary()` function that lists all `CASE_DATA.aid_glossary` term/definition pairs and call it once on page load.
3. Add CSS for `.glossary-term` (underline + cursor), `.tooltip-box` (absolute positioned card), and hover/focus show/hide logic.
4. Update Sub-Tasks 3, 4, 5, and 7 renderers to pipe their text content through `applyTooltips()`.
5. Ensure tooltip positioning doesn't overflow the viewport on small screens.

**Relevant Context:**
- `aid_glossary` rows: `term`, `plain_definition`.
- `applyTooltips()` must be declared before any render function that uses it.
- Use `innerHTML` carefully — only apply tooltip wrapping to text nodes, never to attribute values or HTML tags, to avoid breaking the DOM.
- Tooltip show/hide should work via CSS `:hover` on desktop and a JS `click` toggle on mobile (use a `.tooltip-visible` class toggled by JS).

---

### Sub-Task 7 — Contacts Tab

**Status:** `[ ] pending`

**Intent:**
Render all 4 contact office cards from `CASE_DATA.contacts` in a friendly, scannable layout. This is a public tab — always visible alongside FAQs, Glossary, and Log In — and helps students know exactly who to contact and what to bring before they even log in.

**Expected Outcomes:**
- All 4 contact cards are displayed, each showing: office name, what they help with, hours, contact method, and what to bring/share.
- Cards are laid out in a readable grid or stacked list.
- Contact methods that look like email addresses are rendered as `mailto:` links.
- Glossary terms appearing in card text are wrapped in tooltip spans via `applyTooltips()`.
- Tab is accessible before any student ID is entered (public tab).

**Todo List:**
1. Write a `renderContacts()` function that iterates `CASE_DATA.contacts`.
2. Render each contact as a card with clearly labeled fields, piping text through `applyTooltips()`.
3. Detect email patterns in the `contact` field and wrap them in `<a href="mailto:...">` links.
4. Call `renderContacts()` once on page load (not gated by authentication).

**Relevant Context:**
- `contacts` rows: `office`, `helps_with`, `hours`, `contact`, `bring_or_share`.
- Some `contact` values may contain both a portal link description and an email (e.g., "Portal chat or finaid@example.edu") — parse accordingly.
- This tab was previously named "Get Help" in earlier plan drafts — it is "Contacts" throughout.

---

### Sub-Task 8 — Accessibility and Final Polish

**Status:** `[ ] pending`

**Intent:**  
Ensure the application meets basic WCAG 2.1 AA accessibility standards and applies finishing polish so the experience is consistent, readable, and production-ready.

**Expected Outcomes:**
- All interactive elements have visible focus styles.
- Color contrast for text and badges meets WCAG AA (4.5:1 for normal text, 3:1 for large text).
- The student-ID input has an associated `<label>`.
- Tab panel markup uses `role="tablist"`, `role="tab"`, `role="tabpanel"`, and `aria-selected` / `aria-controls` attributes.
- Accordion items use `aria-expanded`.
- The page title updates to reflect the authenticated student's context.
- The app is usable on mobile (responsive layout, minimum 44px touch targets).

**Todo List:**
1. Add ARIA roles and attributes to the tab bar and tab panels.
2. Add `aria-expanded` to accordion FAQ items.
3. Audit color contrast for all badge and text colors; adjust if needed.
4. Add visible `:focus` outlines to all buttons, links, and inputs.
5. Test responsive layout at 375px viewport width; fix any overflow or cramped elements.
6. Add a meaningful `<title>` and `<meta name="description">` to the `<head>`.

**Relevant Context:**
- No external accessibility library is used — implement ARIA attributes directly in generated HTML strings.
- The tooltip implementation from Sub-Task 6 must also be keyboard-accessible (show on focus, hide on blur).
