# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Personal academic homepage for Yingda Cheng (Professor at Virginia Tech), built with Jekyll on the
**academicpages** template (a fork of Minimal Mistakes). This repo *is* `yingdacheng.github.io` — GitHub
Pages builds and deploys it automatically on every push to `master`. There is no CI, no separate build/deploy
step, no test suite, and no linter/formatter configured.

## Local development

```
bundle install                  # install Ruby deps (delete Gemfile.lock first if it errors)
bundle exec jekyll liveserve    # serve at localhost:4000 with live reload, using _config.dev.yml overrides
```

Ruby-dev, bundler, and nodejs must be installed on the system first. `Gemfile.lock` is intentionally absent
from version control (delete it if it reappears and causes vulnerability warnings).

The `npm run build:js` / `uglify` scripts in `package.json` minify `assets/js/_main.js` (plus vendor plugins)
into `assets/js/main.min.js`. Run this after editing `assets/js/_main.js` — the minified file is what's
actually served.

## Content structure

Standard Jekyll collections, each with its own directory: `_pages`, `_posts`, `_talks`, `_teaching`,
`_publications`, `_portfolio`. Site-wide settings (including the author sidebar with social links) live in
`_config.yml`; `_config.dev.yml` overrides a few values for local serving.

Some collection entries (`_talks`, `_teaching`, `_publications`, `_portfolio`) are still unmodified template
placeholders (e.g. "Talk 1 on Relevant Topic in Your Field"). This is expected and intentional — don't flag
or try to fill these in unless the user specifically asks.

## Generating publication/talk markdown

`markdown_generator/` contains actively-used tooling that converts structured data into the markdown files
these collections expect. Run scripts from inside `markdown_generator/` — they write output to `../_publications/`
or `../_talks/` using relative paths.

- `pubsFromBib.py` — reads `proceedings.bib` and `pubs.bib` (BibTeX, via `pybtex`), writes one `.md` per
  entry to `../_publications/`. Configure via the `publist` dict at the top of the file.
- `publications.py` — reads `publications.tsv` (columns: `pub_date`, `title`, `venue`, `excerpt`, `citation`,
  `site_url`, `paper_url`; `pub_date` as `YYYY-MM-DD`), writes to `../_publications/`.
- `talks.py` — reads `talks.tsv` (columns: `title`, `type`, `url_slug`, `venue`, `date`, `location`,
  `talk_url`, `description`; `date` as `YYYY-MM-DD`), writes to `../_talks/`.

Each `.py` file has a corresponding `.ipynb` with more documentation; they do the same thing.

`talkmap.py` (project root) scrapes the `location` field from every file in `_talks/`, geocodes it via
`geopy`/Nominatim, and regenerates the cluster map in `talkmap/`. Run it from inside `_talks/`. Requires
`glob`, `getorg`, `geopy` (not declared in any manifest — install manually if missing).

## Git workflow

Commit directly to `master` — no branching or PR process for this repo.
