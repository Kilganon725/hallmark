# Understanding: Nutlope/hallmark

> Original project: https://github.com/Nutlope/hallmark
> This fork: Kilganon725/hallmark (public fork, default branch `main` unchanged)
> Original author: Nutlope; README states it is made by Together AI.
> Licence: MIT — original author and licence are retained. See `LICENSE` in the fork.
> Checked: morning of 2026-10-27 (Asia/Shanghai), from GitHub Trending (daily) and the original README.

## 1. What it is

Hallmark is a design skill for coding agents — Claude Code, Codex, Cursor and
OpenCode. Instead of letting a model produce another generic-looking AI page,
it gives the model a concrete design system to follow: a catalogue of
materials (called themes), a hard quality floor, and a lint step that checks
the finished page in a real browser.

In one sentence: it is a set of instructions plus small Node scripts that
steer an agent toward a page that looks designed, not generated.

## 2. Why it is hot

On 2026-10-27 it sat at the top of GitHub Trending for the day, with 2,244
stars that day and about 36.3k stars in total, plus about 1.9k forks.

The timing makes sense: AI-generated landing pages have converged on the same
tells — an eyebrow label above every heading, tracked-capital labels, one
coloured word in a headline, pages of empty frames or photographs only, a
hero that does not fit its screen, an email sign-up at the end of every page,
and a form that says "thanks" without sending anything. Hallmark names those
tells explicitly and turns them into checks, so a developer can see the
problem and fix it instead of only sensing that a page "looks AI-made".

It is also immediately usable: one install command, no new framework to learn,
and it works inside tools developers already use every day.

## 3. How it works at its core

The core is not a large application. It is a skill — instruction files under
`skills/hallmark/` — plus supporting Node scripts, a theme catalogue, an
evaluation folder (`eval/`), and a showcase site (`site/`).

A build runs roughly like this:

1. **Pre-flight.** The skill reads the project (fonts, palette, framework, a
   locked `design.md` if present) and asks once for the real material it
   needs — photos, screenshots, real numbers. Nothing is invented.
2. **Deal a theme, draw the anatomy.** `scripts/theme.mjs deal` offers three
   themes that fit the prompt; `draw` derives the page's structure from the
   job the page must do (a booking page books, a shop sells, a newsletter
   subscribes), with the action placed where that category puts it.
3. **Hero first, reviewed cold.** The hero is built alone, rendered and
   reviewed before the rest of the page exists.
4. **Lint in a real browser.** `scripts/sloplint.mjs --render` checks the
   page at eight widths, and a fresh-context subagent reviews it with
   fresh eyes. Each finished page is self-contained HTML and CSS, with the
   decisions it took stamped in its first CSS comment.

### The three gate tiers

- **Floor — never ships.** Contrast, focus, reduced motion, invented facts,
  and a broken phone layout. These block a page.
- **Reflex — defaults a model falls into.** A build with a reason may
  overrule one, on the record.
- **Finish — craft detail.** Checked, but never blocks shipping.

### Themes: material, not plan

Version 2 ships 48 themes. Each theme is a material — paper, type, colour,
surfaces, its own components and texture — stored as one file of at most
8 KB in `references/themes/`, with a picture of a finished page beside it.
A build reads exactly one theme. Crucially, a theme never says what a page
contains or in what order, so two prompts in the same theme produce two
different pages.

### Redesign and audit

- `/hallmark redesign <path>` rebuilds an existing page while keeping its
  content and wiring; `sloplint --before` records every link, field, handler
  and heading first, and `sloplint --kept` fails the redesign if one went
  missing. The handoff is a side-by-side before and after.
- `/hallmark audit <path>` only reports; it does not change the page.
- Saying **custom** drops the themes entirely: no plan, no components, no
  palette recipe — only the floor, the gates and a short direction to find
  one idea that only this client has.

### Real photographs, separately licensed

Hallmark lists 755 openly licensed photographs in
`skills/hallmark/scripts/photos.json`. It holds no image files itself; the
photos are hosted on Wikimedia Commons and each keeps its own licence
(CC0, public domain, CC BY or CC BY-SA), credited on the page. That photo
licence note is separate from the project's MIT licence, and this fork
does not change it.

## 4. Tech stack

- **Skill instructions** (the main product): markdown instruction files
  under `skills/hallmark/`, consumed by Claude Code, Codex, Cursor and
  OpenCode.
- **Node scripts** (`.mjs`): `theme.mjs` (deal/draw), `sloplint.mjs`
  (static and rendered checks), `install.mjs` (validates every theme and
  reference before copying), and `install-hook.mjs` (an edit-time Floor
  check for Claude Code).
- **Output:** self-contained HTML and CSS pages.
- **Showcase site:** `site/` with `package.json` and `vercel.json`.
- **Requirements to install:** Node 18 or later. Browser checks
  (`sloplint --render`, screenshots) also need `puppeteer-core` installed
  globally and Chrome; without them the static checks still run.
- **Repo shape at check time:** default branch `main`, 297 commits,
  no releases or tags, created 2026-04-27.

## 5. Where it fits

- Landing and marketing pages where a generic look would hurt credibility.
- Job-driven pages: booking, shop, newsletter, API/developer, app and
  studio pages, where the page should end on its action.
- Redesigning or auditing an existing page without losing its content or
  wiring.
- Teams that want a shared, checkable design floor across many small pages,
  rather than a full design system or a new framework.

## 6. Limitations

- It improves the odds; it does not guarantee taste. The fresh-context
  reviewer and the human still matter, and output varies by model and prompt.
- A theme is a material, not a layout plan, so two builds in one theme
  will differ — good for variety, less good for pixel-identical repetition.
- Browser checks need Chrome and `puppeteer-core`; without them only the
  static checks run.
- The photo library's licences are per-photo (CC0 / public domain /
  CC BY / CC BY-SA), not MIT, so reusers must keep the on-page credit.
- The finished page is self-contained HTML/CSS. Integrating it into a
  larger application is a separate step, not something the skill does
  for you.
- The repository is wider than the skill alone (`site/`, `eval/`, plugin
  and workflow files), so a one-day change should stay in documentation
  rather than touching the lint logic without a way to test it properly.

## 7. What this fork changes, and why

This fork makes exactly one commit, with two new documentation files and
no change to skill behaviour:

1. **`UNDERSTANDING.md` (this file).** The research note above, so anyone
   opening the fork can see what the project is, why it trended, how it
   works, and where it falls short — without having to re-derive it.
2. **`README-ZH.md`.** A Chinese quick-start guide: what Hallmark is, how
   to install it, how a build runs, and how redesign/audit work, translated
   and condensed from the original README, with a link back to the original
   project and its MIT licence.

Why this change: Hallmark's audience is global, but its README is English
only, and its value is easiest to miss in a long English skill document.
A short Chinese entry point lowers the barrier for Chinese-speaking
developers to try the one-command install, while changing nothing about
the themes, gates or lint logic — so the change is verifiable against the
README, low-risk, and fits a single day's work. Original authorship and
the MIT licence are preserved; the fork adds documentation, it does not
claim the original work.
