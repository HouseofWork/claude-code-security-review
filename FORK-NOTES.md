# HouseofWork fork of anthropics/claude-code-security-review

Upstream: https://github.com/anthropics/claude-code-security-review (MIT, see LICENSE).
Forked at upstream commit `0c6a49f1fa56a1d472575da86a94dbc1edb78eda` (2026-02-11), the last upstream commit.

## The one change
`action.yml`: `ENABLE_CLAUDE_FILTERING: 'true'` -> `'false'`.

Why: upstream's AI false-positive filter begins with `validate_api_access()`, which calls the retired model
`claude-3-5-haiku-20241022` (retired 2026-02-19). Every run got a 404, and the filter then switched itself off
without saying so. With `'false'` the action no longer makes that failing request, and the behaviour is what it
already was in practice: only the fixed "hard exclusion" rules filter findings. Upstream reports of the same bug:
anthropics/claude-code-security-review issues #103, #123, #127, #139, #154; fix PRs #73, #128, #149 (unmerged).

We did NOT switch the filter to a newer model: it has never run in our environment, and a second model that
decides which findings to hide is a way to lose a real finding in a gate that blocks merges.

## Known limits we kept (not changed here)
The hard exclusion rules (and the action's own prompt) drop whole classes of finding: generic DoS/resource
exhaustion, "missing rate limiting", SSRF, open redirects, memory-safety and regex-injection reports, and
findings in Markdown files. A per-PR review built on this action is not a substitute for a deeper audit of those classes.

## Also not pinned by this action (upstream design)
It installs `@anthropic-ai/claude-code` and a few Python packages unpinned at run time, and sets up Node 18, where npm
falls back to an old Claude Code release that cannot use the 5.x models.

## Syncing
Upstream is unmaintained as of this fork. If it resumes, rebase this one commit onto it and re-pin consumers by SHA.
