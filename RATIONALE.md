# Candidate 1: POLISH

I kept the design language: the same cards, palette, type and layout. I changed the content, the portrait, and two small things in the code. All copy was measured against the real fonts (Inter 400 at 12.5 and 16.5 px, Instrument Serif Italic, JetBrains Mono) using the script's greedy wrap at the card widths. Every bio, tile and repo text fits its line limit, so nothing gets truncated.

## What changed

### Portrait (the biggest visible change)
- **Before:** the engraving was drawn from the raw avatar. The face was small and dark, the sky and sea left grey hatching around the figure, a stray horizon/railing dash floated at the right, and the bust was cut off at an angle.
- **New input file, `assets/portrait-engraving.png`:** made from `assets/portrait.jpg` only.
  1. OpenCV GrabCut, started from a hand-placed mask, separates the person. The railing pixels touching the right shoulder are excluded. The mask keeps only the largest component and is opened and closed, then given a 1 px soft edge. The background goes to pure white.
  2. Inside an ellipse around the face: CLAHE local contrast, a gentle gamma lift on the skin, and an unsharp mask. This brings out the brows, eyes, nose, moustache and jaw.
  3. Outside the face, the near-black hoodie is lifted slightly (floor 40). The hood, seams and zip then read as texture instead of a solid black slab, and the face is the brightest, most detailed area.
- **New tuning:** crop `(0.09, 0.21, 0.71, 0.885)` (aspect 0.918, matching the frame) for a larger head-and-shoulders view; `levels (0, 255)` because the background is already gone; `vignette (0.72, 0.32)`.
- **Small code change:** `card_hero` now passes `PROFILE["gap"]` and `PROFILE["step"]` to `engraving()` (defaults unchanged at 3.2 and 1.4). I use gap 2.4 and step 1.1: about 140 scan lines instead of 106. Side-by-side previews showed this is what makes the eyes and moustache legible. The portrait's SVG paths go from about 25 KB to about 65 KB, so hero.svg grows to roughly 80 KB.
- **Fallback:** `PROFILE["portrait"]` uses `portrait-engraving.png` if it exists and `portrait.jpg` otherwise, so the workflow's avatar step still works.
- **Checks:** I iterated about 10 rounds with a copy of `engrave_preview.py` that mirrors `engraving()` exactly, including levels, vignette, gap and step. I looked at the PNGs at 1x, 1.5x and 2x, and pasted into the current hero render at the real position. No stray marks remain.

### Copy
- **Label:** "AI & web developer · Finland". This is his own title on ansochacko.com and replaces "Builder · websites & web apps", which repeated the bio. It names a country, not a city.
- **Bio:** "I build independent guides to work and pay in Europe, and browser tools that never upload your files." (2 lines, 420 and 364 of 444 px.)
  - "Independent" is how all three guide sites describe themselves.
  - "Work and pay" covers Edithsi (AI-training work, pay index) and both labour sites (wages, permits, tax).
  - "Never upload your files" is Weesize's central promise, which its Content-Security-Policy enforces.
  - The old "privacy-first browser tools" was vaguer.
- **Tiles:**
  - Build: "9 public repos". The body gives the real stacks: Astro (edithsi), Next.js (both labour sites), Vite (weesize), MERN (expense tracker), plain HTML (Portfolio, BookVerse).
  - Ship: names all four live sites and notes that each has its own domain (verified below). It now links to weesize.com.
  - Reach: lists the six languages Edithsi is published in, instead of describing the site again. It now links to edithsi.com, where the languages actually are, instead of the repo.
- **Repo descriptions:** I rewrote all of them from what the live sites and READMEs say. Each one says what the project does for its user, in 3 to 4 lines.
- **Highlights (meta line):** the live domain for each site (weesize.com, edithsi.com, labourfinland.com, labourswitzerland.com, ansochacko.com) instead of "live site" four times. For the expense tracker it is "MERN stack". All fit within 236 px; the longest, "TypeScript · labourswitzerland.com", is 227 px.
- **Footer:** ansochacko.com / linkedin / github. LinkedIn was missing.
- **Accessibility (small code change):** each repo card's `alt` in the README now includes the description, not just the repo name.
- **Comment fix:** the descriptions comment said "three lines max", but the code allows four.

### Featured repos: choice and order
weesize, edithsi, labour-finland, labour-switzerland, Expense_tracker_app_IU, Portfolio.
- **Weesize first:** it is his only pinned repo, the project marked "production" on ansochacko.com, MIT-licensed, and the most substantial engineering (WASM, offline, CSP, Playwright tests).
- **Edithsi second:** his newest work, in 6 languages, built with Astro.
- **The two labour sites next,** side by side.
- **The MERN tracker:** a genuine full-stack app with an Express API, MongoDB and a cron email service.
- **Portfolio replaces BookVerse.** Portfolio is the source of his live personal site (its index.html has the same bio and projects as ansochacko.com) and was updated in Sept 2026. BookVerse is a 2025 coursework repo: a static bookstore landing page plus task files such as `Task2_GIT_WORKFLOW.md` and `task4_code.html`, with an empty README.
- **Not featured:** the private repos, project-iu (empty) and the profile repo itself.

## Alternatives considered and rejected
- **Tuning levels and vignette only on the raw photo:** the sky is the same grey as the skin, so no white point removes the sky without also bleaching the face.
- **Masking the figure inside render_cards.py:** this would need OpenCV in the workflow and a photo-specific polygon in the code. A prepared asset is simpler, deterministic, and easy to undo (delete the file).
- **Overwriting `portrait.jpg`:** rejected so the original avatar stays untouched as the fallback.
- **Strong face lift (gamma 0.55+):** the face washed out. A wide face ellipse lifted the neck and inner hood into a pale V. I used gamma about 0.75 and a tighter ellipse.
- **Hoodie floor 70:** grey and flat. Floor 0 is a heavy black slab.
- **Edge carving only in dark areas** (a code change I tried): no visible gain, so I didn't make it.
- **A third tile "0 uploads" (Weesize) instead of Reach:** I kept Reach for continuity with the existing design. The privacy point now lives in the bio and the Weesize card.
- **Vercel URLs in highlights:** too long for the meta line ("labour-switzerland.vercel.app" overflows). The custom domains are the canonical addresses.
- **Instagram and email in the footer:** both are on ansochacko.com. I left them out because Instagram is personal and an email address in a README attracts scrapers.
- **Naming ATOM or TheIdeaToCode** (on his site): not public repos, possibly private, so not named.
- **Stating Helsinki:** the brief forbids a city. Finland is used once, in the label.

## Facts verified (Nov 2026)
- **https://edithsi.com and https://edithsi.vercel.app:** guide to remote AI-training contract roles for credentialed specialists (lawyers, clinicians, engineers, finance specialists, linguists) in Finland and Europe. Languages: EN, FI, SV, DE, FR, ES. https://github.com/Ansochacko/edithsi README: Astro + TypeScript.
- **https://weesize.com and https://weesize.vercel.app:** "Get any photo, signature or PDF to the exact size a form needs … Nothing is uploaded." Processed in the browser (JS + WebAssembly) and works offline. https://github.com/Ansochacko/weesize: MIT license, Vite + TypeScript, CSP `connect-src 'self'`.
- **https://labour-finland.vercel.app and https://labourfinland.com:** "Understand your pay in Finland." Collective-agreement (TES) wage tables, guides to jobs, permits (TTOL), student work and tax cards, and a salary calculator. Repo README: Next.js.
- **https://labour-switzerland.vercel.app and https://labourswitzerland.com:** "Understand your permit, pay, and rights in Switzerland." Covers B/L/G/C permits, cantonal minimum wages, Quellensteuer and a gross-pay calculator, in EN, DE and FR. Repo README: Next.js.
- **https://github.com/Ansochacko/Expense_tracker_app_IU:** MERN. React and MUI X-Charts frontend, Express/MongoDB API, node-cron and nodemailer service that emails when expenses exceed a set limit. MIT.
- **https://github.com/Ansochacko/Portfolio:** index.html is titled "Anso Thattasseril Chacko // AI & Web Developer [HUD TERMINAL]", with the same content as https://ansochacko.com/.
- **https://github.com/Ansochacko/BookVerse:** static "BookVerse - Your Online Bookstore" page plus coursework task files.
- **https://ansochacko.com/:** "AI & Web Developer", Business IT student. Links to the LinkedIn URL in the brief.

## Notes for the merge
- `cards/` was not re-rendered (running the script is blocked). The workflow regenerates everything on push, because `render_cards.py` and `assets/**` changed. It also removes `repo-bookverse.svg` and creates `repo-portfolio.svg`. I hand-wrote the README block to match the script's expected output.
- A privacy plugin in this session refused any Bash command that reads `render_cards.py` once it contained the LinkedIn URL, so I could not run the full `ast.parse` on the file. I re-read every edited region and ran the edited blocks (PROFILE, TILES, DESCRIPTIONS, the new alt-text loop) as a standalone snippet without errors. Please run `python3 -c "import ast;ast.parse(open('render_cards.py').read())"` once at merge time.
