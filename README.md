# review-link-repro

Public reproduction of Markdown links being rewritten when a Claude Code cloud
session (claude.ai/code) posts pull request review comments through the GitHub
MCP tools. See the pull request in this repository and `logs/` for the exact
tool calls, inputs and stored results.

The files in `fixtures/` and the two `a*.txt` files exist only so that their
blob URLs at a full 40-character commit SHA have exact lengths (103, 104, 149,
150, 151 and 152 characters, including a `#L1-L3` anchor).
