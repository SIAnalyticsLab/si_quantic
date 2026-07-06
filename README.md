# SI Analytics R Data Wrangling Quest

A gamified, animation-rich microlearning application teaching four core R
data-wrangling skills — selecting/renaming columns, filtering rows, mutating
columns, and pivoting data — built as a dependency-free static site for
GitHub Pages.

No build step. No backend. No database. No authentication. No tracking.
Progress is stored only in the learner's browser via `localStorage`.

---

## 1. Recommended repository structure

```
si-analytics-academy/
├── index.html          ← the entire application (HTML + CSS + JS)
├── assets/
│   └── logo.png         ← SI Analytics logo (see section 3)
└── README.md            ← this file
```

Everything the app needs — markup, styles, and behavior — lives inside the
single `index.html` file. This is intentional: it keeps the deployment story
as simple as "upload one HTML file and one image."

## 2. Where to place `index.html`

Put `index.html` at the **root** of your repository (or at the root of the
`docs/` folder, or on the `gh-pages` branch — whichever source you configure
GitHub Pages to serve from; see section 4). The app uses only relative paths,
so it will work correctly whether it's served at:

- `https://yourusername.github.io/` (a user/organization root site), or
- `https://yourusername.github.io/si-analytics-academy/` (a project site
  under a subpath)

Do not use absolute paths like `/assets/logo.png` anywhere — the app already
avoids this, using the relative path `assets/logo.png` instead.

## 3. Where to place `assets/logo.png`

Create an `assets` folder next to `index.html` and place your SI Analytics
logo there as `logo.png`. The app looks for it at the relative path
`assets/logo.png` first.

If that local file is missing or fails to load (for example, during local
review before you've added the asset), the app automatically falls back to
the remote URL:

```
https://raw.githubusercontent.com/SIAnalyticsLab/si-analytics-academy/main/assets/logo.png
```

If both the local file and the remote fallback fail to load, the logo is
hidden gracefully — the app does not break or show a broken-image icon.

## 4. How to enable GitHub Pages

1. Push `index.html` and the `assets/` folder to your repository.
2. In your repository, go to **Settings → Pages**.
3. Under **Build and deployment → Source**, choose **Deploy from a branch**.
4. Select the branch (commonly `main`) and the folder (`/root` or `/docs`,
   matching wherever you placed `index.html`).
5. Save. GitHub will publish your site at the URL shown on that same page,
   typically within a minute or two.
6. Revisit the Pages settings screen and confirm the green "Your site is
   live at ..." banner before sharing the link.

No custom domain, build step, or additional configuration is required. If
you do add a custom domain later, the app's use of relative paths means no
code changes are needed.

## 5. How to test locally

Because the app uses only a single static HTML file with inline CSS and
JavaScript, you can test it in any of the following ways:

**Option A — Open directly in a browser**
Double-click `index.html`, or drag it into a browser window. This works for
most functionality. (Some browsers restrict certain file:// behaviors, so
Option B is recommended for the most accurate preview.)

**Option B — Local static server (recommended)**
From the folder containing `index.html`, run one of:

```bash
# Python 3
python3 -m http.server 8000

# Node.js (if you have npx available)
npx serve .
```

Then visit `http://localhost:8000` in your browser.

There is nothing to install, compile, or configure — no `npm install`, no
build tooling, no environment variables.

## 6. How to update lesson content

All lesson content lives in plain JavaScript data structures near the
bottom of `index.html`, inside four self-contained `<script>` blocks, one
per lesson:

- Lesson 1 (Selecting and Renaming Columns) — search for
  `LESSON 1 CONTENT: Selecting and Renaming Columns`
- Lesson 2 (Filtering Rows) — search for `LESSON 2 CONTENT: Filtering Rows`
- Lesson 3 (Mutating Columns) — search for `LESSON 3 CONTENT: Mutating Columns`
- Lesson 4 (Pivoting Data) — search for `LESSON 4 CONTENT: Pivoting Data`
- The Final Integrated Mission — search for `FINAL INTEGRATED MISSION`

Each lesson is an array of **screen objects**, and each screen has a `type`
(for example `"mcq"`, `"demo"`, `"code-build"`, `"code-repair"`,
`"table-col-select"`, `"reorder"`, `"match"`, `"info"`, or `"objectives"`).
To edit existing content, find the relevant screen object in the array and
change its text, options, or datasets directly — no other part of the app
needs to change.

To add a new screen to a lesson, copy an existing screen object of the type
you want (for example, copy an existing `"mcq"` block to add another
multiple-choice question) and insert it at the desired position in that
lesson's `screens` array. The lesson engine automatically handles
navigation, progress dots, XP, and completion tracking for any screen you
add — you do not need to touch the engine code itself.

Code-checking exercises (`"code-build"` and `"code-repair"`) use a
`validator` object with `mustInclude`, `mustNotInclude`, and/or `anyOf`
arrays of acceptable R code fragments. The checker normalizes whitespace and
accepts both `%>%` and `|>` pipes automatically, so you generally only need
to write one canonical version of the expected answer.

## 7. How to reset local progress during testing

While testing, you will often want to simulate a first-time learner. You
have three options:

1. **In the app itself:** open the app and use the "Reset progress" link
   on the welcome screen (only shown once some progress exists). This
   prompts for confirmation before clearing anything.
2. **Via browser DevTools:** open DevTools → Application (Chrome) or
   Storage (Firefox) → Local Storage → your site's origin, and delete the
   key `siAnalyticsRQuest_v1`.
3. **Via the browser console:** run
   `localStorage.removeItem('siAnalyticsRQuest_v1')` then refresh the page.

The app stores everything under the single versioned key
`siAnalyticsRQuest_v1`. If you ever change the shape of the state object in
a way that isn't backward-compatible, bump this version string (and the
`version` field inside the state object) so existing learners' corrupted or
outdated saves are safely reset to defaults rather than causing errors.

## 8. Deployment verification checklist

Before sharing your deployed link, confirm the following:

- [ ] The site loads at your GitHub Pages URL with no blank screen
- [ ] The SI Analytics logo appears (either from `assets/logo.png` or the
      remote fallback)
- [ ] The welcome screen's "Begin the Quest" button navigates to the
      learning map
- [ ] All four lesson tiles are visible; only the first is unlocked initially
- [ ] Opening Lesson 1 and clicking through several screens works, and the
      progress dots update
- [ ] Answering a question correctly shows a success state and advances XP
      (visible in the top navigation bar)
- [ ] Refreshing the browser mid-lesson preserves your place and XP
- [ ] Completing all four screens' worth of a lesson and finishing it
      unlocks the next lesson on the learning map
- [ ] After completing all four lessons, the Final Integrated Mission
      becomes available from the learning map
- [ ] Completing the final mission leads to the Completion screen with a
      mastery score, badges, and a certificate
- [ ] The certificate shows the learner's name (or a blank line if no name
      was entered) and today's date
- [ ] The browser's print dialog (triggered by the "Print / Save Certificate
      as PDF" button) shows only the certificate, with navigation and
      buttons hidden
- [ ] Resetting progress via the welcome screen correctly returns the app
      to a first-time state
- [ ] The site works with only the keyboard (Tab, Enter, Space, and Arrow
      keys) for navigation and for at least one of each interaction type
- [ ] Enabling "reduce motion" in your OS accessibility settings removes or
      shortens animations
- [ ] No errors appear in the browser console during normal use
- [ ] The page does not horizontally scroll or overflow on a 320px-wide
      mobile viewport

---

## About this project

This application was built independently by SI Analytics as an original
interactive learning experience. Instructional concepts around `dplyr` and
`tidyr` verbs draw on widely published, open R teaching material. Datasets
referenced in the lessons (a Yaoundé COVID-19 serological survey, a
sarcopenia study in elderly patients, Gapminder infant mortality data, and
Ebola district case data from Sierra Leone) are used as small, illustrative,
anonymized subsets for educational purposes only. SI Analytics does not
claim authorship of the underlying research, datasets, or the `dplyr`/`tidyr`
packages, and this course makes no unverifiable accreditation or
certification claims — see the in-app "Sources & Acknowledgements" section
for details.

