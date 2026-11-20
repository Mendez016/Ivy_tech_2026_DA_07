# My Financial Aid Portal
### Ivy Tech Community College — DA-07 Prototype | 2026

## Architecture created by IBM Bob

<img width="3600" height="2471" alt="data_diagram_2026" src="https://github.com/user-attachments/assets/5f6414a5-16ac-49ab-8fe6-37dccbc37e75" />

---

## Table of Contents

1. [The Problem](#1-the-problem)
2. [The Solution](#2-the-solution)
3. [The AI and Technical Approach](#3-the-ai-and-technical-approach)
4. [Pillar and Statewide Strategy](#4-pillar-and-statewide-strategy)
5. [How IBM Bob Was Used](#5-how-ibm-bob-was-used)
6. [2030 Impact Statement](#6-2030-impact-statement)
7. [Bob Collaboration Log](#7-bob-collaboration-log)

---

## 1. The Problem

**Persona:** First-generation college student navigating financial aid for the first time.

First-generation students arrive at Ivy Tech without a family model for how financial aid works. When the institution sends a verification notice, a Satisfactory Academic Progress suspension, or a loan requirement, the student often does not understand what it means, what action is required, or what the consequence of inaction will be. The language in formal notices is regulatory and dense. Deadlines are communicated in separate systems. The result is that students miss documents, miss deadlines, and find their aid cancelled or their classes dropped for non-payment — not because they were unwilling to act, but because they did not know what to do or when.

The two measurable outcomes this problem produces are:

- **Aid files not completed on time.** Documents such as verification worksheets and tax transcripts are submitted late or not at all, deferring or cancelling disbursement.
- **Administrative drops for non-payment.** When aid is on hold and the student does not resolve it before the payment deadline, the institution removes the student from enrolled courses.

Both outcomes are preventable if the student has a clear, plain-language view of their own status and a direct path to the office that can help them.

---

## 2. The Solution

**My Financial Aid Portal** is a client-side web application that gives each student a personalized, plain-language view of their financial aid standing. The student enters their Student ID, and the portal immediately presents:

**My Status panel**
A prioritized checklist of every requirement and award on file for that student. Items that require action appear first, sorted by deadline. Each outstanding item includes a plain-language explanation of what to do, the deadline, the consequence of missing it, and a direct link to the responsible office. A progress bar shows how many items are complete. At the top of the panel, a notification block confirms the email address on file for reminder communications and restates the student's ID, so the student knows which account they are viewing.

**Deadlines panel**
A chronological calendar of all institutional deadlines for the aid year. Deadlines that are directly relevant to the logged-in student's status are highlighted. The student can see at a glance which dates apply to them and which are general.

**Your Notices section**
Every formal notice sent to the student is listed with its subject and date. The student can expand any notice to read the full text. Glossary terms that appear in the notice body are underlined and show a plain-language definition on hover or focus, so the student does not need to leave the page to understand regulatory language.

**FAQs panel**
Ten common financial aid questions answered in plain language, covering the FAFSA, disbursement timing, verification, SAP, loans, payment plans, and dropping classes. Accessible to all users without logging in.

**Glossary panel**
Definitions for ten financial aid terms: verification, SAP, SAP appeal, disbursement, Pell Grant, subsidized loan, entrance counseling, master promissory note, Title IV, and administrative withdrawal. Terms link back from notices and status items so the student encounters definitions in context.

**Contacts panel**
Direct contact information for Financial Aid, the Bursar, Advising, and the Student Emergency Fund. When a student clicks a contact reference inside their status checklist, the portal navigates directly to that office's card and briefly highlights it. This keeps the path to a human short and explicit, in keeping with the A-Ivy principle that AI supports people and does not replace them.

**Design constraints honored**
- No real student or employee data. All records are sample data only.
- No server, no login backend, no data transmitted anywhere. The app runs entirely in the browser from static files.
- Full keyboard navigation. All interactive elements are reachable by Tab and activated by Enter or Space.
- Screen-reader compatible. Live regions, ARIA roles, and descriptive labels are used throughout.
- Responsive layout. The interface adapts to narrow screens for students on mobile devices.

---

## 3. The AI and Technical Approach

**Technology stack**

The application is a single HTML file (`index.html`) with an inline CSS block and an inline JavaScript block, backed by one data file (`case_data.js`). There are no build tools, no package manager, no frameworks, and no external dependencies. The entire prototype runs by opening `index.html` in a browser.

**Data model**

All sample data lives in `case_data.js` as `window.CASE_DATA`, a single JSON object with six arrays:

| Array | Purpose |
|---|---|
| `aid_status` | One row per student per aid item, with status, deadline, instructions, and office |
| `notices` | Formal notices sent to each student, with subject, date, and body text |
| `deadline_calendar` | Institution-wide deadline events for the aid year |
| `aid_glossary` | Term and plain-language definition pairs |
| `faqs` | Question and answer pairs |
| `contacts` | Office name, hours, contact method, and what to bring |
| `students_email` | Student ID to email address mapping for reminder notification display |

This structure mirrors the CSV source files in the repository so that the data layer could be replaced with a real API call in a future iteration without changing the rendering logic.

**Rendering approach**

Each panel is rendered by a dedicated JavaScript function (`renderStatus`, `renderNotices`, `renderDeadlines`, `renderFAQs`, `renderGlossary`, `renderContacts`) that reads from `window.CASE_DATA` and writes HTML into a container element. Functions run once at login for the authenticated panels and once at page load for the public panels. No reactive framework is needed at this scale.

**Glossary tooltip system**

`applyTooltips` scans any rendered HTML string for glossary terms and wraps matches in a styled `<span>` with ARIA attributes. On pointer or keyboard focus, a positioned tooltip box shows the plain-language definition. This runs entirely in the client with no external library.

**Tab navigation system**

The portal uses the ARIA tab pattern. A single `activateTab` function manages `aria-selected`, `tabindex`, and `display` state across all panels. Keyboard arrow-key navigation between tabs is supported.

**AI approach (IBM Bob)**

IBM Bob served as the primary development partner across all four parts of the build. The AI role was generative and iterative: Bob produced scaffolding, data structures, rendering functions, and accessibility patterns based on plain-language prompts, and the human team reviewed, tested, and directed each step. No AI output was accepted without human review. Bob did not make architectural decisions independently; it responded to defined requirements and the team chose among options.

---

## 4. Pillar and Statewide Strategy

**Strategic Plan 2030 alignment**

| Level | Value |
|---|---|
| Pillar | Pillar 3: Student-Centered Journey |
| Strategy | Strategy 4: Operationalize Holistic Student Supports |
| A-Ivy focus area | Around-the-clock student navigation and support |

**Pillar 3** calls for a seamless, unified experience so every learner can access clear roadmaps, efficient systems, timely academic and career support services, and transparent communication at every stage. This portal addresses the transparent communication and efficient systems components directly: a student can reach their complete aid picture, every notice, and the contact for every outstanding item in one session, at any time of day.

**Strategy 4** specifically targets holistic student supports — meaning proactive, coordinated help that meets students where they are rather than requiring them to navigate multiple offices independently. The portal operationalizes this by aggregating status, notices, deadlines, and contacts into a single authenticated view and by surfacing the human contact point inside every action item.

**A-Ivy principles applied**

- AI supports people and does not replace them. Every outstanding item includes a named office and a direct contact path. The student is never left with AI-generated advice and no human to follow up with.
- Answers are grounded in approved sources. All content in the portal comes from the provided sample dataset. No AI-generated financial aid guidance appears as portal content.
- Personal data is protected. No student data is transmitted, stored, or logged. The session exists only in browser memory and clears on sign-out.
- A person remains in charge of high-impact decisions. Aid eligibility, appeal outcomes, and payment arrangements are handled by Financial Aid and the Bursar. The portal surfaces information and directs students to those humans; it does not make or simulate institutional decisions.
- Designed for accessibility. Keyboard navigation, ARIA roles, live regions, sufficient color contrast, and responsive layout are all present in the prototype.

**Seed metric targets**

The two metrics identified in Part 1 that this solution would move by 2030:

1. **Aid files completed on time** — measured as the percentage of students with outstanding verification or loan requirements who submit all documents before the institutional deadline. The portal reduces the information gap between notice and action by making the requirement, deadline, and submission path visible in one place.
2. **Administrative drops for non-payment** — measured as the number of students dropped from courses each semester due to unresolved aid holds or unpaid balances. The portal surfaces balance and hold status prominently and links directly to the Bursar, reducing the likelihood that a student is unaware of an impending drop.

---

## 5. How IBM Bob Was Used

IBM Bob was used as the primary technical collaborator throughout all four parts of the build. The following describes how the team and Bob worked together.

**Part 1 — Discover and Define**

The team provided Bob with the persona, problem statement, seed idea, and statewide strategy. Bob was asked to turn the starter user story into a build plan and project scaffold. Bob produced a file structure, a data model, a list of panels to build, and a set of measurable metrics. The team reviewed and selected the two metrics (aid files completed on time; drops for non-payment) and confirmed the panel set before any code was written.

**Part 2 — Build with IBM Bob**

Bob generated the full initial prototype: the HTML structure, CSS design system, JavaScript rendering functions, ARIA tab pattern, login flow, glossary tooltip engine, and the `case_data.js` data file. Each function was described in a plain-language prompt and Bob produced the implementation. The team tested each panel in the browser, identified gaps, and issued follow-up prompts to correct them. Bob also authored the architecture diagram referenced at the top of this README.

**Part 3 — The Roadblocks**

The team received pilot feedback and directed Bob to address specific findings within the existing build. Bob made targeted edits to the rendering functions and data layer without restructuring the prototype. The team reviewed every diff before accepting it.

**Part 4 — Finalize and Showcase**

Bob was used to write this README, structure the 2030 Impact Statement section, and produce the collaboration log below. All content was reviewed and approved by the team before commit.

---

## 6. 2030 Impact Statement

**Who benefits**

First-generation students at Ivy Tech Community College who receive financial aid and are at risk of losing it due to missed requirements or unresolved holds. These students are disproportionately affected by information gaps because they cannot rely on family experience with financial aid systems. The portal is designed specifically for this population, though it is usable by any enrolled student.

**Which metrics move and by roughly how much**

Ivy Tech serves approximately 90,000 students annually. Financial aid offices at community colleges commonly report that 10 to 15 percent of students with outstanding verification requirements miss the deadline each semester. If a plain-language status portal reduces that rate by one-third — a conservative estimate given that the primary barrier is information access rather than willingness — roughly 3,000 to 4,500 aid files per year would be completed on time that otherwise would not be.

For administrative drops for non-payment, the same information-gap assumption applies. Students who are unaware that their aid is on hold cannot act to resolve it. Making hold status visible alongside the contact path for resolution could reduce drops meaningfully within the first full semester of deployment. Even a 10 percent reduction in non-payment drops represents hundreds of students retaining enrollment each year.

**What it would take to roll this out across Ivy Tech's campuses by 2030**

The current prototype is a static client-side application running on sample data. A production deployment would require the following:

1. **Data integration.** Connect the portal to Ivy Tech's student information system (SIS) so that aid status, notices, and balances are pulled in real time for authenticated students. This is the largest technical dependency.
2. **Authentication.** Replace the sample Student ID login with Ivy Tech's existing SSO (single sign-on) system so that students authenticate with their institutional credentials and the portal displays their actual records.
3. **Hosting.** Deploy the application within Ivy Tech's existing web infrastructure or student portal environment. Because the front end is static HTML and JavaScript, the hosting requirement is minimal once the data API is in place.
4. **Content review.** Have Financial Aid and the Bursar review all plain-language explanations, deadlines, and contact information before go-live. The glossary and FAQ content in the prototype was written for demonstration purposes and must be validated against current institutional policy.
5. **Accessibility audit.** Commission a formal accessibility review against WCAG 2.1 AA before go-live, particularly for screen-reader compatibility with dynamically rendered content.
6. **Phased rollout.** Begin with one campus or one cohort (for example, students selected for verification in one semester), measure the impact on file completion rates, and expand based on results.

The timeline from integration work to full campus rollout is estimated at two to three semesters, placing a statewide launch well within the 2030 horizon.

**The one risk to watch**

The portal reduces information friction but does not reduce the actual burden of action. A student who understands that they need to upload a tax transcript still needs to request that transcript from the IRS, obtain it, and submit it correctly. If the institutional processes for document submission are themselves inaccessible or time-consuming, the portal will surface the problem clearly but the student will still not complete the requirement. The risk is that the tool creates a false sense of sufficiency — that making status visible is enough — when the deeper barrier for some students is capacity, not information. The watch metric is the percentage of students who view their outstanding items in the portal but do not complete them before the deadline. If that figure is high, the next intervention is process simplification, not more communication.

---

## 7. Bob Collaboration Log

The following is a chronological record of how the team directed IBM Bob during this build. Each entry describes the prompt given, what Bob produced, and what the team did with the output.

| Session | Prompt summary | Bob output | Team action |
|---|---|---|---|
| Part 1 | Provided persona, problem, seed idea, and strategy. Asked Bob to generate a build plan and project scaffold. | File structure, data model, panel list, two measurable metrics, starter user story expanded into acceptance criteria. | Team confirmed the two metrics and panel set. Approved scaffold. |
| Part 2, step 1 | Asked Bob to generate the HTML shell, CSS design system, and tab navigation structure. | Full `index.html` with inline CSS, ARIA tab pattern, header, and empty panel containers. | Team reviewed in browser. Confirmed layout and color system. |
| Part 2, step 2 | Asked Bob to generate `case_data.js` from the provided CSV files. | `window.CASE_DATA` object with all six arrays populated from sample records. | Team verified row counts against CSV files. Approved. |
| Part 2, step 3 | Asked Bob to build the login flow, `renderStatus`, and `renderNotices` functions. | JavaScript functions implementing student lookup, auth state management, status checklist with progress bar, and expandable notice list. | Team tested all three sample student IDs. Identified tooltip gap. Issued follow-up prompt. |
| Part 2, step 4 | Asked Bob to build the glossary tooltip engine and wire it into status and notice rendering. | `applyTooltips`, `showTip`, `hideTip`, `positionTip` functions; tooltip CSS; ARIA markup on glossary terms. | Team tested on mobile viewport. Confirmed keyboard accessibility. Approved. |
| Part 2, step 5 | Asked Bob to build `renderDeadlines`, `renderFAQs`, `renderGlossary`, and `renderContacts`. | Four rendering functions, deadline relevance logic, accordion FAQ and glossary patterns, contact grid with highlight-on-navigate behavior. | Team tested each panel. Approved. |
| Part 3, roadblock 1 | Provided pilot feedback. Asked Bob to address at least two findings within the existing build without starting over. | Targeted edits to rendering functions and data layer responding to the specific findings. | Team reviewed each diff. Accepted two of three changes. |
| Part 3, roadblock 2 | Asked Bob to add email notification display to the My Status panel tied to student ID. | Plan presented first. After approval: `students_email` array added to `case_data.js`; `renderStatus` extended to prepend reminder block. | Team reviewed plan before commit. Approved implementation. |
| Part 4 | Asked Bob to write the full README with all five required sections, the 2030 Impact Statement, and this collaboration log. | This document. | Team reviewed all content against the prompt requirements. Approved for commit. |
