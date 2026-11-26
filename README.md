# review-link-repro

Public reproduction: when a Claude Code cloud session (claude.ai/code) posts to
GitHub through the GitHub MCP tools, every URL of 150 characters or more is
stored wrapped in a backtick code span, which breaks Markdown links. URLs of 149
characters or fewer are stored unchanged.

Observed 2026-11-26 from a Claude Code cloud session (Claude Code 2.1.332,
Anthropic-hosted environment, started from the iOS app). Possibly the same bug as
[anthropics/claude-code#105858](https://github.com/anthropics/claude-code/issues/105858).

## Result

| Case | Write tool | URL lengths | Stored |
| --- | --- | --- | --- |
| 0 | `create_pull_request` (PR description) | 149, 150 | 150 wrapped, 149 unchanged |
| 1 | `add_comment_to_pending_review` | 103 | unchanged |
| 2 | `add_comment_to_pending_review` | 104 | unchanged |
| 3 | `add_comment_to_pending_review` | 149 | unchanged |
| 4 | `add_comment_to_pending_review` | 150 | wrapped in 2 backticks |
| 5 | `add_comment_to_pending_review`, three links in one body | 150, 151, 152 | wrapped in 2, 3 and 4 backticks |
| 6 | `pull_request_review_write` `submit_pending` (review body) | 149, 150 | 150 wrapped, 149 unchanged |
| 7 | `add_issue_comment` (PR conversation comment) | 149, 150 | 150 wrapped, 149 unchanged |

- The boundary is 149/150 characters, the same as in #105858. The 104-character
  boundary reported in
  [github/github-mcp-server#3299](https://github.com/github/github-mcp-server/issues/3299)
  did not apply here: 104 and 149 were stored unchanged.
- A link is stored as `[text](`` URL ``)`. Each further long URL in the same
  body gets a run one backtick longer (2, 3, 4, ...).
- The inline comments were already rewritten while the review was still
  pending, before `submit_pending`. So the rewrite happens on each write call,
  not at submission.
- It is the stored content, not a display or read-path effect: the PR
  description read back through the REST search API (`gh api search/issues`),
  outside the MCP server, matches the MCP read byte for byte, and GitHub renders
  the wrapped links as code.
- Every write call returned success. Nothing said the body had been changed.

## Files

- [`logs/sent-vs-stored.md`](logs/sent-vs-stored.md): each body as sent and as
  stored.
- [`logs/github-calls.md`](logs/github-calls.md) and
  [`logs/github-calls.jsonl`](logs/github-calls.jsonl): every GitHub call made
  for this reproduction (git clone and push, MCP tool calls, the search API
  read), with exact inputs, outputs and UTC timestamps, exported from the
  session transcript. Local paths outside this repository are redacted.
- [`scripts/make_bodies.py`](scripts/make_bodies.py): regenerates the exact
  bodies for a commit SHA.
- `a1.txt`, `a12.txt` and `fixtures/`: files whose blob URLs at a full commit
  SHA with `#L1-L3` are exactly 103, 104, 149, 150, 151 and 152 characters.

## Reproduce

1. In a Claude Code cloud session with this repository attached, open a pull
   request that adds the fixture files.
2. Run `python3 scripts/make_bodies.py <head sha>`.
3. Create a pending review pinned to the head SHA
   (`pull_request_review_write` `create`), add cases 1 to 5 with
   `add_comment_to_pending_review` on line 2 (case 5: lines 1 to 3), then
   submit with case 6 as the body and `event: COMMENT`. Post case 7 with
   `add_issue_comment`.
4. Read back with `pull_request_read` (`get_review_comments`, `get_reviews`,
   `get_comments`) and compare with what was sent.
