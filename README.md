# Course QA Review

A single-file, static web app for browsing course content audit findings —
built for reviewing e-learning videos and courses (technical accuracy,
terminology, gaps, code issues, etc.) in plain, easy-to-understand language.

No backend, no build step, no dependencies to install. It's one HTML file
that runs entirely in the browser.

## Live demo

Open `course-qa-review.html` directly in a browser, or host it as a static
site (see **Deploying to GitHub Pages** below).

## What it does

- Lists every finding from a course audit as a card: what's wrong, why it
  matters, and the suggested fix — written in plain language, not jargon.
- For a single video, shows a **timeline strip** across the runtime with a
  colored marker at each finding's timestamp — click a marker to jump
  straight to that finding.
- For a full course (no single runtime), skips the timeline and shows a
  flat, filterable list instead.
- **Filter** by severity (Critical / High / Medium / Low / Missing content)
  by clicking the legend chips.
- **Search** findings by keyword.
- **Sort** by video order or by severity.
- **Mark reviewed** checkboxes to track progress — saved to your browser's
  local storage, per course, per browser. Nothing is sent anywhere or
  shared with anyone else viewing the page.
- Light/dark theme toggle (top right), and it also respects your system's
  color scheme automatically.
- A **course switcher** at the top lets you flip between multiple audited
  courses/videos in the same app.

## Self-service analysis (new)

The app now has a **"+ New analysis"** tab. It lets you paste a transcript
(or load a `.txt`/`.srt` file) and calls the Claude API directly from your
browser, using your own Anthropic API key, to generate a first-pass set of
findings — without needing to come back to a chat conversation.

**Important limitations, on purpose:**

- **This does not work on a claude.ai artifact preview link.** Anthropic's
  sandboxed preview environment blocks pages from calling its own API
  directly, for security reasons. This feature only works once the file is
  deployed somewhere normal — GitHub Pages, any static host, or opened
  locally in a browser.
- **It's a lighter pass, not a full audit.** One AI read-through of the
  text you paste — no video frame extraction, no fact-checking against
  outside sources, no code execution. Good for a fast first draft; treat
  the output as a starting point to verify, not a final report.
- **Text only.** It can't process `.mp4` video or `.tar`/`.zip` course
  archives directly — only plain text or `.srt` content.
- **It's billed to your own Anthropic account.** The API key you enter is
  used only in your browser (sent directly to `api.anthropic.com`) and is
  never written into the file itself. Checking "remember this key on this
  device" stores it in that browser's local storage, on that device only.

Findings generated this way are stored per-browser (in `localStorage`)
alongside your other courses, and show up as a removable tab — there's a
"Remove this analysis" button if you want to clear one out.

If you want the deeper, verified kind of audit (the one behind the
Word2Vec and SQLedX entries), that still happens in a full conversation —
paste the same transcript there instead of using this tab.

## Adding a new course or video

All content lives in one place: the `COURSES` array near the top of the
`<script>` block in `course-qa-review.html`. Open the file in any text
editor and add a new object to the array:

```js
{
  id: "unique-id",                 // used internally, keep it short and unique
  org: "Publisher name",           // shown as a chip, e.g. "IBM Skills Network"
  name: "Course or video title",
  runtimeLabel: "5:55 runtime",    // free text — shown as a chip
  runtimeSeconds: 355,             // total seconds if it's a single video with a
                                    // timeline; set to null for a multi-video course
  status: "reviewed",              // "reviewed" or "pending"
  findings: [
    {
      id: "f1",
      seconds: 38,                // timestamp in seconds, or null for a general/
                                    // non-timestamped finding
      timecode: "0:38",           // display label matching `seconds`, or null
      severity: "critical",       // "critical" | "high" | "medium" | "low" | "gap"
      category: "Example used",   // short free-text tag shown on the card
      title: "Short, plain-language issue title",
      description: "2–4 sentences explaining the issue in plain language.",
      why: "Why this matters for a learner.",
      fix: "What to change to fix it."
    }
    // ...more findings
  ]
}
```

A course with `status: "pending"` and an empty `findings` array shows up
in the switcher as "not audited yet" — useful for tracking what's queued
up without needing findings yet.

No other files need to change — the UI (timeline, filters, search, cards)
builds itself from whatever's in `COURSES`.

## Deploying to GitHub Pages

1. Push `course-qa-review.html` to a GitHub repo (rename it to `index.html`
   if you want it served at the root of your Pages site).
2. In the repo: **Settings → Pages** → choose the branch and folder to
   serve from → Save.
3. GitHub will publish it at `https://<username>.github.io/<repo>/`
   (or `.../course-qa-review.html` if you kept the original filename).

No build step or GitHub Action is required — it's a static file.

## Notes

- Fonts (IBM Plex Sans / IBM Plex Mono) load from Google Fonts over the
  network. This works fine on GitHub Pages or any normal web host. If you
  need it to work fully offline, download the font files and update the
  `<link>` tags in the `<head>` to point to local copies instead.
- "Mark reviewed" state is stored in the browser's `localStorage`, scoped
  to whatever domain/URL is serving the page. Progress won't carry over
  between, say, a local file, a claude.ai preview, and a GitHub Pages URL —
  each is a separate storage location.
- Everything is plain HTML/CSS/vanilla JavaScript — no framework, no
  npm packages, no build tooling.

## License

Add whichever license fits your use — nothing in this file assumes one.
