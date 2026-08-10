# Dashvanth Healthcare — Website

The public website for Dashvanth Healthcare, East Delhi's multispeciality
centre. Built with [Astro](https://astro.build) — content lives in plain
JSON data files, separate from page layout, so non-technical staff can edit
text through the CMS instead of touching code.

## How editing works

- Contact details, hours, nav, footer → `src/data/site.json`
- Homepage hero/stats/why-choose-us/how-it-works → `src/data/home.json`
- About page → `src/data/about.json`
- Departments (all 7, incl. Gynaecology) → `src/data/departments.json`
- Doctors (3 full profiles + 2 "Joining Soon" placeholders) → `src/data/doctors.json`
- Testimonials → `src/data/testimonials.json`
- Blog categories → `src/data/blog-categories.json`
- Blog articles → `src/data/blog-articles.json`
- Page layout (header, footer, mobile nav, styling) → `src/layouts/Layout.astro` and `src/styles/global.css`

Every department and doctor page, and every blog article page, is generated
automatically from these JSON files via Astro's `getStaticPaths()` — adding a
new department/doctor/article to the JSON automatically creates its page and
adds it to the relevant listing/index page. No new files need to be created
by hand for new content that fits the existing shape.

### Adding a new blog article

Add an entry to `src/data/blog-articles.json` with a unique `slug`, a
`category` matching one of the slugs in `blog-categories.json`
(`orthopaedics`, `neurosurgery`, `paediatrics`, `diagnostics`), a `title`,
`reviewedBy`, a `blocks` array (see existing articles for the `h2`/`h3`/`p`/
`ul`/`table` block shapes), and an optional `faqs` array. It will
automatically appear on `/blog/<category>/` and be rendered at
`/blog/<category>/<slug>/`.

Only 10 sample articles (across 4 categories) have been migrated so far, by
the client's explicit choice — the old site listed up to 20 titles per
category as a roadmap. Add the rest over time via the CMS; do not bulk-invent
content.

## Running it locally

Requires [Node.js](https://nodejs.org) (v18+).

```bash
npm install       # first time only
npm run dev        # starts a local preview at http://localhost:4321
npm run build       # builds the production site into dist/
```

## Putting it on GitHub

1. Create a new empty repository on [github.com](https://github.com) (don't add a README there).
2. In this project folder:

```bash
git init
git add .
git commit -m "Initial site"
git branch -M main
git remote add origin https://github.com/<your-username>/<repo-name>.git
git push -u origin main
```

## Auto-deploying

**Netlify**
1. Go to [app.netlify.com](https://app.netlify.com) → "Add new site" → "Import an existing project".
2. Connect your GitHub repo.
3. Build command: `npm run build`  ·  Publish directory: `dist`
4. Deploy, then attach the `dashvanthhealthcare.com` domain under Site settings → Domain management.

## Visual editor (Decap CMS)

This project includes [Decap CMS](https://decapcms.org) at `/admin/`, with
collections for Site Settings, Homepage Content, About Page, Departments,
Doctors, Testimonials, Blog Categories, and Blog Articles. Editors log in
with GitHub, edit fields, hit "Publish", and it commits the JSON files to
GitHub, which triggers a Netlify rebuild.

Setup mirrors the companion personal-doctor site:
1. Create a GitHub OAuth App with callback URL `https://api.netlify.com/auth/done`.
2. Add its Client ID/Secret under Netlify → Project configuration → Access &
   security → **OAuth** → Install provider → GitHub.
3. `public/admin/config.yml` already points at the `github` backend — update
   the `repo:` value to match wherever this repo actually lives before going
   live.

**Known CMS limitation:** the "table" content block used in two diagnostics
articles (HbA1c, and the reference table format) only exposes table headers
in the CMS editor — table row data must be edited directly in
`src/data/blog-articles.json` for now, since Decap's list widget doesn't
cleanly support nested list-of-lists editing. This does not block publishing;
it's a minor authoring inconvenience for that one block type.
