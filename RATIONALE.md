# Candidate 2: own identity

Aim: the profile should stop looking like a re-skin of madhavbuilds (bright blue/red/yellow,
"Every day, in blue", Build/Ship/Reach) and read as Anso's own, with the same level of craft.
The layout, card code and animation are unchanged.

## What changed

### Palette (`C`, `HEAT`): Nordic nature
| role | before | after | check |
|---|---|---|---|
| paper | `#f4f3ef` warm | `#f3f4f1` cool birch white | |
| muted text | `#7a7870` | `#646b6e` | 4.9:1 on paper (was about 4.0) |
| accent (square, arrows, best month, italic heading words) | blue `#2e54ff` | fjord `#1a6470` | 6.1:1 on paper |
| tile 1 / dot 1 | blue | fjord `#1a6470` | white text 6.8:1 |
| tile 2 / dot 2 | red `#ec3a25` | lingonberry `#a8263c` | white text 7.0:1 |
| tile 3 / dot 3 | yellow `#fdc626` | cloudberry `#eba93c` | ink text 8.7:1 |
| heatmap | 5 blues | `#e1e6e3 #b6d5d6 #7db4ba #3f8791 #155663` (ice to fjord) | |
| engraving ink | `#2a2925` | `#1d2427` | |

Fjord, lingonberry and cloudberry tie the colours to Finland and the Nordics without leaning on
flag clichés. The heatmap ramp is petrol/teal, so it is neither madhavbuilds' blue nor GitHub's
green. I checked the best-month bar next to the ink bars: the fjord bar is clearly teal, and it
also gets an accent-coloured number above it.

### Headings
- Heatmap title: "Every day, *in blue*" became "Built *in the open*". It fits a public
  contribution graph, every repo shown is public, and Weesize is MIT-licensed.
- "What I'm coding" became "Live sites *& earlier builds*". This is honest about the six cards:
  four deployed sites, then two 2025 projects.
- The italic words now use the accent. The GitHub dark/light `<picture>` variants each get their
  own accent: `#7fc4cc` on dark (9.6:1 on `#0d1117`) and `#1a6470` on light (6.8:1 on white).
  The upright words keep GitHub's own text colours.
- Both headings now live in the content block (`HEAT_TITLE`, `CODING_TITLE`), and the README alt
  text comes from `CODING_TITLE`.

### Hero
- Label "Builder · websites & web apps" became "Web developer · Finland & Europe". It names a
  region, not a city.
- Bio (2 lines, measured with the real Inter metrics at 444 px): "I build independent guides to
  work in Finland and Europe, and browser tools that never upload your files."
- Portrait: the face is now clearly legible and the sky, sea and railing are gone.
  - `assets/portrait.jpg` is now a prepared cut-out. I ran OpenCV GrabCut locally (rect
    init, largest component, 1.2 px feather), composited the figure onto pure white, and applied
    gamma 0.6 to lift the skin mid-tones so the face separates from the near-black hoodie. The
    original is his GitHub avatar and stays in git history. If the file is deleted, the workflow
    fetches the avatar again.
  - `crop` (0.12, 0.19, 0.64, 0.755) is a tighter head-and-shoulders crop. The face goes from
    about 26 to about 45 scan lines, and the ratio stays at 0.92.
  - `levels` (15, 205) and `vignette` (0.80, 0.30). The background is already white, so the
    vignette only softens the shoulders.
  - New `PROFILE["gap"] = 2.6` (line spacing). `card_hero` passes it to `engraving()` (default
    3.2). At 3.2 the eyes and mouth broke up when shown at 1.5x. At 2.6 the brows, eyes, nose,
    moustache and lips all read. Cost: the engraving grows from about 46 KB to about 58 KB.
  - I iterated with a copy of `engrave_preview.py` (plus a `gap` parameter), rendered with
    `rsvg-convert` and looked at the PNGs at 1x and 1.5x. I tried about 14 variants: CLAHE
    (blew out the cheek), the uncut photo (sea and sky stay as grey bands), and several level and
    crop combinations.

### Tiles (each tells a part of his story)
| label | big | body | link |
|---|---|---|---|
| Independent (fjord) | 3 guides | Edithsi, Labour Finland and Labour Switzerland: work, pay and permits, explained. | labour-finland.vercel.app |
| Private by design (lingonberry) | 0 uploads | Weesize shrinks photos and PDFs to a form's size limit, on your own device. | weesize.vercel.app |
| Multilingual (cloudberry) | 6 languages | Edithsi in English, Finnish, Swedish, German, French and Spanish. | edithsi.vercel.app |

I measured every body with Inter metrics: at most 3 lines, at most 227 of the 232 px.
Code change: the tile text colour is now chosen by WCAG luminance (`is_light()`), not by
`colour == "yellow"`. Any colour key in `C` therefore works with readable text, and the soft
label becomes ink at 80% on light tiles. `{repos}` also works in a tile body now.

### Repo cards
I rewrote the descriptions from what each project actually does (all fit in 4 lines at 236 px):
- edithsi: AI-training work guide for professionals; roles, pay, platform comparison, six
  languages.
- weesize: exact file size for forms, runs in the browser, nothing uploaded; highlight
  "live site · MIT".
- labour-finland: collective-agreement pay for 17 occupations, gross-pay calculator, guides,
  sourced figures.
- labour-switzerland: permits, pay benchmarks, gross-pay calculator, cantonal tax, EN/DE/FR.
- Expense_tracker_app_IU: MERN app with charts and a node-cron service that emails when
  spending passes a limit.
- BookVerse: it had no description. It turned out to be a one-page HTML/CSS bookstore mock-up,
  so the card says exactly that.

Dots cycle fjord / lingonberry / cloudberry.

### Footer and README
- Added LinkedIn to the footer. It is public on his GitHub profile.
- I updated the README block to match what the script will write: new tile slugs
  `tile-guides`, `tile-private` and `tile-languages`, the new alt texts and the footer. The old
  `cards/tile-{build,ship,reach}.svg` are stale. `main()` deletes every `cards/*.svg` and
  re-renders on the first workflow run, which a push to `render_cards.py`/`assets/**` triggers.
- The credit to @madhavbuilds is untouched. `python3 -c "import ast;..."` parses.
- `render_cards.py` was not run. I checked colours and text lengths with my own swatch/mock SVGs
  and fontTools measurements, outside the candidate folder.

## Alternatives considered and rejected
- **Finnish flag blue/white palette**: too literal, and still a blue accent, which is the
  re-skin problem again.
- **Aurora green heatmap**: too close to GitHub's default green.
- **Keeping the key names blue/red/yellow with new values**: less code churn, but
  `"blue": "#1a6470"` would mislead whoever edits it next. I renamed them to
  `accent`/`fjord`/`lingon`/`cloudberry` (5 references).
- **Tile "{repos} repos" (dynamic)**: dropped for "0 uploads", which says something only Anso's
  work says.
- **Heading "Study projects"**: "IU" and the "Task2/Task4" file names suggest coursework, but I
  could not confirm it, so I used "earlier builds" (both are from 2025).
- **Replacing BookVerse with Portfolio**: I could not confirm that Portfolio is the source of
  ansochacko.com (2 commits, no description), so I kept BookVerse with an accurate description.
- **Masking the background inside `engraving()`**: the workflow installs only Pillow and has no
  segmentation, so a pre-cut asset is simpler and more reliable.
- **Naming ATOM / TheIdeaToCode** (listed on ansochacko.com): not public repos here, so they are
  not mentioned.
- **"Helsinki"**: it appears on his site and on Edithsi, but per the brief no city is stated.
- **Saying Edithsi is unsponsored**: rejected. The site discloses referral links, so the copy
  says "independent" only, which is how the site describes itself.

## Facts verified
- Edithsi: https://edithsi.vercel.app: independent guide to remote AI-training work aimed at
  European professionals; languages EN, FI, SV, DE, FR, ES; open roles, field pages, platform
  comparison and pay index, guides. Canonical domain is edithsi.com.
- Weesize: https://weesize.vercel.app: "Get any photo, signature or PDF to the exact size a
  form needs"; processing in the browser (JS + WebAssembly), nothing uploaded, works offline;
  free. https://github.com/Ansochacko/weesize: MIT licence, Vite + TypeScript, pdf-lib,
  pdfjs, MozJPEG WASM.
- Labour Finland: https://labour-finland.vercel.app: "Understand your pay in Finland"; 17
  occupations, salary/gross-pay calculator, 7 guides; sources include union agreements, Migri
  and Statistics Finland; independent of government and unions.
- Labour Switzerland: https://labour-switzerland.vercel.app: permits (B/L/G/C), wage
  benchmarks, gross-pay calculator, cantons and tax; EN/DE/FR; independent of SEM/SECO/cantons.
- Expense tracker: https://github.com/Ansochacko/Expense_tracker_app_IU: React/Vite frontend
  with MUI X-Charts, Express/MongoDB API, node-cron + nodemailer alert service.
- BookVerse: https://github.com/Ansochacko/BookVerse and its raw `index.html`: static
  "BookVerse - Your Online Bookstore" page, hero and 10 featured-book cards, CSS hover, no JS.
- Portfolio: https://github.com/Ansochacko/Portfolio: 4 files, no description (not featured).
- https://ansochacko.com/: describes him as an AI & web developer; Weesize described as
  privacy-first and on-device.
