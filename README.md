# Repo, Explained

Fully automated site: every day at 09:30, a launchd job picks the hottest recently-created GitHub repo not yet covered, has Claude Code (headless) research it, generate a Scrimba visual explainer plus a written article, rebuilds the static site, and pushes to GitHub Pages.

## How it works

```
launchd (daily 09:30)
  └─ scripts/run_daily.sh
       ├─ scripts/pick_repo.py      # GitHub search API: hottest repo <21 days old, ≥500★, not covered
       ├─ claude -p prompts/daily.md # research → Scrimba explainer → data/posts/<date>-<slug>.json
       ├─ scripts/build_site.py     # data/posts/*.json → docs/ (index + post pages)
       └─ git commit + push         # GitHub Pages serves docs/
```

## Operate it

- Run once manually: `scripts/run_daily.sh` (logs to `logs/<date>.log`)
- Pause: `launchctl bootout gui/$(id -u)/io.trending-explained.daily`
- Resume: `launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/io.trending-explained.daily.plist`
- Monetization: paste an EthicalAds/Carbon snippet into the `AD SLOT` comment in `scripts/build_site.py`, rebuild.
- Custom domain: add a `CNAME` file in `docs/` and point DNS at GitHub Pages.

## Notes / limitations

- The machine must be awake (or wake later — launchd runs missed jobs on wake) and logged in for the daily run.
- The Scrimba Explain MCP connector must be reachable in headless mode; if not, posts publish without an explainer link (`explainer_url: null`) and you can backfill from an interactive session.
- Each daily run consumes Claude usage from your plan.
