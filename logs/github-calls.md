# GitHub calls made for this reproduction

Exported from the Claude Code cloud session's own transcript (tool inputs and results verbatim, as recorded by the session). Times are UTC. Only calls to GitHub for this repository are included; local paths outside this repository are replaced with `<primary-working-dir>` and `<scratchpad>`. Machine-readable copy: [`github-calls.jsonl`](github-calls.jsonl).

## 1. `Bash`: git clone (read)

- Called: 2026-11-26T09:33:03.847433Z
- Returned: 2026-11-26T09:33:06.460091Z

Input:

~~~~~json
{
  "command": "git clone --depth 1 https://github.com/Nerlant/review-link-repro /home/user/review-link-repro 2>&1 | tail -5; git -C /home/user/review-link-repro rev-parse HEAD && git -C /home/user/review-link-repro log --format='%H %an %s' && ls -la /home/user/review-link-repro && git -C /home/user/review-link-repro branch -a",
  "description": "Clone the new test repository",
  "timeout": 600000
}
~~~~~

Output:

~~~~~text
Cloning into '/home/user/review-link-repro'...
eafe6b9d6cdf58d9f53d470514581b4895bf7fcd
eafe6b9d6cdf58d9f53d470514581b4895bf7fcd Nerlant Initial commit
total 52
drwxr-xr-x 3 root root  4096 Nov 26 09:33 .
drwxr-xr-x 4 root root  4096 Nov 26 09:33 ..
drwxr-xr-x 8 root root  4096 Nov 26 09:33 .git
-rw-r--r-- 1 root root 35149 Nov 26 09:33 LICENSE
-rw-r--r-- 1 root root    19 Nov 26 09:33 README.md
* main
  remotes/origin/HEAD -> origin/main
  remotes/origin/main
~~~~~

## 2. `Bash`: create fixtures, commit, git push (refused)

- Called: 2026-11-26T09:33:36.188417Z
- Returned: 2026-11-26T09:33:40.646579Z

Input:

~~~~~json
{
  "command": "cd /home/user/review-link-repro && git checkout -q -b repro/review-links && python3 -I - <<'EOF'\nimport os\nbase = \"https://github.com/Nerlant/review-link-repro/blob/\" + \"0\"*40 + \"/\"\nanchor = \"#L1-L3\"\npaths = {103: \"a1.txt\", 104: \"a12.txt\"}\nfor n in (149, 150, 151, 152):\n    name_len = n - len(base) - len(anchor) - len(\"fixtures/\") - len(\".txt\")\n    stem = f\"url-{n}-chars-\"\n    paths[n] = \"fixtures/\" + stem + \"x\" * (name_len - len(stem)) + \".txt\"\nos.makedirs(\"fixtures\", exist_ok=True)\nfor n, p in sorted(paths.items()):\n    url = base + p + anchor\n    assert len(url) == n, (n, len(url))\n    with open(p, \"w\") as f:\n        f.write(f\"Fixture file {p}\\nIts blob URL at a full commit SHA with {anchor} is exactly {n} characters long.\\nLine 3.\\n\")\n    print(n, p)\nEOF\ncat > README.md <<'EOF'\n# review-link-repro\n\nPublic reproduction of Markdown links being rewritten when a Claude Code cloud\nsession (claude.ai/code) posts pull request review comments through the GitHub\nMCP tools. See the pull request in this repository and `logs/` for the exact\ntool calls, inputs and stored results.\n\nThe files in `fixtures/` and the two `a*.txt` files exist only so that their\nblob URLs at a full 40-character commit SHA have exact lengths (103, 104, 149,\n150, 151 and 152 characters, including a `#L1-L3` anchor).\nEOF\ngit add -A && git -c user.name=\"Claude\" -c user.email=\"noreply@anthropic.com\" commit -q -m \"test(repro): add fixtures with exact-length blob URLs\n\nCo-Authored-By: Claude Opus 5.5 <noreply@anthropic.com>\nClaude-Session: https://claude.ai/code/session_01JuGY2gyMPzp4knqjsAtUv7\" && git log --oneline -2 && git -c push.negotiate=true push -u origin repro/review-links 2>&1 | tail -15",
  "description": "Create fixture files, commit, and try pushing branch"
}
~~~~~

Output:

~~~~~text
103 a1.txt
104 a12.txt
149 fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt
150 fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt
151 fixtures/url-151-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxx.txt
152 fixtures/url-152-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxxx.txt
6e7bdc8 test(repro): add fixtures with exact-length blob URLs
eafe6b9 Initial commit
remote: Claude doesn't have GitHub access to Nerlant/review-link-repro for your organization. An org admin can install the Claude GitHub App at https://github.com/apps/claude/installations/select_target, or reconnect GitHub from claude.ai settings (https://claude.ai/customize/connectors?auth_start=github&auth_start_force=1) to re-link an existing installation
fatal: unable to access 'https://github.com/Nerlant/review-link-repro/': The requested URL returned error: 403
Shell cwd was reset to <primary-working-dir>
~~~~~

## 3. `mcp__github__list_branches`

- Called: 2026-11-26T09:33:49.232880Z
- Returned: 2026-11-26T09:33:50.002544Z

Input:

~~~~~json
{
  "owner": "Nerlant",
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
[{"name":"main","sha":"eafe6b9d6cdf58d9f53d470514581b4895bf7fcd","protected":false}]
~~~~~

## 4. `mcp__github__create_branch`

- Called: 2026-11-26T09:33:52.861944Z
- Returned: 2026-11-26T09:33:53.881450Z  (error)

Input:

~~~~~json
{
  "branch": "repro/review-links",
  "from_branch": "main",
  "owner": "Nerlant",
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
failed to create branch: POST https://api.github.com/repos/Nerlant/review-link-repro/git/refs: 403 Resource not accessible by integration []
~~~~~

## 5. `Bash`: git push (retry after the GitHub App was installed)

- Called: 2026-11-26T09:36:36.900890Z
- Returned: 2026-11-26T09:36:42.194929Z

Input:

~~~~~json
{
  "command": "cd /home/user/review-link-repro && git -c push.negotiate=true push -u origin repro/review-links 2>&1 | tail -8; git rev-parse HEAD",
  "description": "Retry pushing fixture branch to test repo"
}
~~~~~

Output:

~~~~~text
remote: 
remote: Create a pull request for 'repro/review-links' on GitHub by visiting:        
remote:      https://github.com/Nerlant/review-link-repro/pull/new/repro/review-links        
remote: 
To https://github.com/Nerlant/review-link-repro
 * [new branch]      repro/review-links -> repro/review-links
branch 'repro/review-links' set up to track 'origin/repro/review-links'.
6e7bdc80e2a244654c297b34eb5e593ebf8c2aef
Shell cwd was reset to <primary-working-dir>
~~~~~

## 6. `mcp__github__create_pull_request`

- Called: 2026-11-26T09:37:15.169810Z
- Returned: 2026-11-26T09:37:17.907301Z

Input:

~~~~~json
{
  "base": "main",
  "body": "Case 0 (PR description, `create_pull_request`): [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3) and [url-150](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3).\n\nReproduction of Markdown links being rewritten when a Claude Code cloud session posts to GitHub through the GitHub MCP tools. The review on this PR holds cases 1 to 6 and the conversation comment holds case 7. Each case states the length of its URLs.\n\n🤖 Generated with [Claude Code](https://claude.com/claude-code)\n\nhttps://claude.ai/code/session_01JuGY2gyMPzp4knqjsAtUv7",
  "draft": false,
  "head": "repro/review-links",
  "owner": "Nerlant",
  "repo": "review-link-repro",
  "title": "test(repro): reproduce rewritten links in review comments"
}
~~~~~

Output:

~~~~~text
{"id":"5507548834","url":"https://github.com/Nerlant/review-link-repro/pull/1"}
~~~~~

## 7. `mcp__github__pull_request_review_write`

- Called: 2026-11-26T09:37:21.673638Z
- Returned: 2026-11-26T09:37:23.008088Z

Input:

~~~~~json
{
  "commitID": "6e7bdc80e2a244654c297b34eb5e593ebf8c2aef",
  "method": "create",
  "owner": "Nerlant",
  "pullNumber": 1,
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
pending pull request created
~~~~~

## 8. `mcp__github__add_comment_to_pending_review`

- Called: 2026-11-26T09:37:27.759875Z
- Returned: 2026-11-26T09:37:30.146050Z

Input:

~~~~~json
{
  "body": "Case 1 (inline, `add_comment_to_pending_review`): one link, URL of 103 characters: [a1.txt](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/a1.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_",
  "line": 2,
  "owner": "Nerlant",
  "path": "a1.txt",
  "pullNumber": 1,
  "repo": "review-link-repro",
  "side": "RIGHT",
  "subjectType": "LINE"
}
~~~~~

Output:

~~~~~text
pull request review comment successfully added to pending review
~~~~~

## 9. `mcp__github__add_comment_to_pending_review`

- Called: 2026-11-26T09:37:29.800857Z
- Returned: 2026-11-26T09:37:32.215531Z

Input:

~~~~~json
{
  "body": "Case 2 (inline, `add_comment_to_pending_review`): one link, URL of 104 characters: [a12.txt](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/a12.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_",
  "line": 2,
  "owner": "Nerlant",
  "path": "a12.txt",
  "pullNumber": 1,
  "repo": "review-link-repro",
  "side": "RIGHT",
  "subjectType": "LINE"
}
~~~~~

Output:

~~~~~text
pull request review comment successfully added to pending review
~~~~~

## 10. `mcp__github__add_comment_to_pending_review`

- Called: 2026-11-26T09:37:37.656144Z
- Returned: 2026-11-26T09:37:39.620241Z

Input:

~~~~~json
{
  "body": "Case 3 (inline, `add_comment_to_pending_review`): one link, URL of 149 characters: [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_",
  "line": 2,
  "owner": "Nerlant",
  "path": "fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt",
  "pullNumber": 1,
  "repo": "review-link-repro",
  "side": "RIGHT",
  "subjectType": "LINE"
}
~~~~~

Output:

~~~~~text
pull request review comment successfully added to pending review
~~~~~

## 11. `mcp__github__add_comment_to_pending_review`

- Called: 2026-11-26T09:37:40.852935Z
- Returned: 2026-11-26T09:37:42.870244Z

Input:

~~~~~json
{
  "body": "Case 4 (inline, `add_comment_to_pending_review`): one link, URL of 150 characters: [url-150](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_",
  "line": 2,
  "owner": "Nerlant",
  "path": "fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt",
  "pullNumber": 1,
  "repo": "review-link-repro",
  "side": "RIGHT",
  "subjectType": "LINE"
}
~~~~~

Output:

~~~~~text
pull request review comment successfully added to pending review
~~~~~

## 12. `mcp__github__add_comment_to_pending_review`

- Called: 2026-11-26T09:37:45.006450Z
- Returned: 2026-11-26T09:37:47.206448Z

Input:

~~~~~json
{
  "body": "Case 5 (inline, multi-line, `add_comment_to_pending_review`): three links of 150, 151 and 152 characters in one body, in the sentence shapes of the original review: first ([url-150](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3)), then ([url-151](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-151-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3)). Last ([url-152](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-152-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3)), end.\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_",
  "line": 3,
  "owner": "Nerlant",
  "path": "fixtures/url-151-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxx.txt",
  "pullNumber": 1,
  "repo": "review-link-repro",
  "side": "RIGHT",
  "startLine": 1,
  "startSide": "RIGHT",
  "subjectType": "LINE"
}
~~~~~

Output:

~~~~~text
pull request review comment successfully added to pending review
~~~~~

## 13. `mcp__github__pull_request_read`

- Called: 2026-11-26T09:37:53.070456Z
- Returned: 2026-11-26T09:37:54.087154Z

Input:

~~~~~json
{
  "method": "get_review_comments",
  "owner": "Nerlant",
  "perPage": 20,
  "pullNumber": 1,
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
{"review_threads":[{"id":"PRRT_kwDOV8tcXc68Tqi5","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 1 (inline, `add_comment_to_pending_review`): one link, URL of 103 characters: [a1.txt](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/a1.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"a1.txt","line":2,"original_line":2,"author":"Nerlant","created_at":"2026-11-26T09:37:29Z","updated_at":"2026-11-26T09:37:29Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706367533"}],"total_count":1},{"id":"PRRT_kwDOV8tcXc68Tqmu","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 2 (inline, `add_comment_to_pending_review`): one link, URL of 104 characters: [a12.txt](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/a12.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"a12.txt","line":2,"original_line":2,"author":"Nerlant","created_at":"2026-11-26T09:37:31Z","updated_at":"2026-11-26T09:37:31Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706367942"}],"total_count":1},{"id":"PRRT_kwDOV8tcXc68TqzV","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 3 (inline, `add_comment_to_pending_review`): one link, URL of 149 characters: [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt","line":2,"original_line":2,"author":"Nerlant","created_at":"2026-11-26T09:37:39Z","updated_at":"2026-11-26T09:37:39Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706369378"}],"total_count":1},{"id":"PRRT_kwDOV8tcXc68Tq4i","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 4 (inline, `add_comment_to_pending_review`): one link, URL of 150 characters: [url-150](`` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ``)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt","line":2,"original_line":2,"author":"Nerlant","created_at":"2026-11-26T09:37:42Z","updated_at":"2026-11-26T09:37:42Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706369973"}],"total_count":1},{"id":"PRRT_kwDOV8tcXc68TrA3","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 5 (inline, multi-line, `add_comment_to_pending_review`): three links of 150, 151 and 152 characters in one body, in the sentence shapes of the original review: first ([url-150](`` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ``)), then ([url-151](``` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-151-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ```)). Last ([url-152](```` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-152-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ````)), end.\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"fixtures/url-151-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxx.txt","line":3,"original_line":3,"start_line":1,"original_start_line":1,"author":"Nerlant","created_at":"2026-11-26T09:37:46Z","updated_at":"2026-11-26T09:37:46Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706370850"}],"total_count":1}],"totalCount":5,"pageInfo":{"hasNextPage":false,"hasPreviousPage":false,"startCursor":"Y3Vyc29yOnYyOpK0MjAyNi0xMS0yNlQwOTozNzoyOVrOvE6ouQ==","endCursor":"Y3Vyc29yOnYyOpK0MjAyNi0xMS0yNlQwOTozNzo0NlrOvE6wNw=="}}
~~~~~

## 14. `mcp__github__pull_request_read`

- Called: 2026-11-26T09:37:53.254508Z
- Returned: 2026-11-26T09:37:54.214935Z

Input:

~~~~~json
{
  "method": "get",
  "owner": "Nerlant",
  "pullNumber": 1,
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
{"number":1,"title":"test(repro): reproduce rewritten links in review comments","body":"Case 0 (PR description, `create_pull_request`): [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3) and [url-150](`` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ``).\n\nReproduction of Markdown links being rewritten when a Claude Code cloud session posts to GitHub through the GitHub MCP tools. The review on this PR holds cases 1 to 6 and the conversation comment holds case 7. Each case states the length of its URLs.\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","state":"open","draft":false,"merged":false,"mergeable_state":"clean","html_url":"https://github.com/Nerlant/review-link-repro/pull/1","user":{"login":"Nerlant","id":17651174,"profile_url":"https://github.com/Nerlant","avatar_url":"https://avatars.githubusercontent.com/u/17651174?v=4"},"head":{"ref":"repro/review-links","sha":"6e7bdc80e2a244654c297b34eb5e593ebf8c2aef","repo":{"full_name":"Nerlant/review-link-repro"}},"base":{"ref":"main","sha":"eafe6b9d6cdf58d9f53d470514581b4895bf7fcd","repo":{"full_name":"Nerlant/review-link-repro"}},"additions":28,"deletions":1,"changed_files":7,"commits":1,"created_at":"2026-11-26T09:37:16Z","updated_at":"2026-11-26T09:37:16Z"}
~~~~~

## 15. `mcp__github__pull_request_review_write`

- Called: 2026-11-26T09:38:08.703473Z
- Returned: 2026-11-26T09:38:11.522067Z

Input:

~~~~~json
{
  "body": "Case 6 (review body, `pull_request_review_write` submit_pending): [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3) and [url-150](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3).\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_",
  "event": "COMMENT",
  "method": "submit_pending",
  "owner": "Nerlant",
  "pullNumber": 1,
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
pending pull request review successfully submitted
~~~~~

## 16. `mcp__github__add_issue_comment`

- Called: 2026-11-26T09:38:10.299382Z
- Returned: 2026-11-26T09:38:12.820485Z

Input:

~~~~~json
{
  "body": "Case 7 (PR conversation comment, `add_issue_comment`): [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3) and [url-150](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3).\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_",
  "issue_number": 1,
  "owner": "Nerlant",
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
{"id":"7188260651","url":"https://github.com/Nerlant/review-link-repro/pull/1#issuecomment-7188260651"}
~~~~~

## 17. `mcp__github__pull_request_read`

- Called: 2026-11-26T09:38:16.893061Z
- Returned: 2026-11-26T09:38:17.914888Z

Input:

~~~~~json
{
  "method": "get_reviews",
  "owner": "Nerlant",
  "pullNumber": 1,
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
[{"id":6071674850,"state":"COMMENTED","body":"Case 6 (review body, `pull_request_review_write` submit_pending): [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3) and [url-150](`` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ``).\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#pullrequestreview-6071674850","user":{"login":"Nerlant","id":17651174,"profile_url":"https://github.com/Nerlant","avatar_url":"https://avatars.githubusercontent.com/u/17651174?u=66c81bc954a9ea17e9761f0065e518019060312c\u0026v=4"},"commit_id":"6e7bdc80e2a244654c297b34eb5e593ebf8c2aef","submitted_at":"2026-11-26T09:38:10Z","author_association":"OWNER"}]
~~~~~

## 18. `mcp__github__pull_request_read`

- Called: 2026-11-26T09:38:18.293537Z
- Returned: 2026-11-26T09:38:19.155206Z

Input:

~~~~~json
{
  "method": "get_review_comments",
  "owner": "Nerlant",
  "perPage": 20,
  "pullNumber": 1,
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
{"review_threads":[{"id":"PRRT_kwDOV8tcXc68Tqi5","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 1 (inline, `add_comment_to_pending_review`): one link, URL of 103 characters: [a1.txt](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/a1.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"a1.txt","line":2,"original_line":2,"author":"Nerlant","created_at":"2026-11-26T09:37:29Z","updated_at":"2026-11-26T09:38:10Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706367533"}],"total_count":1},{"id":"PRRT_kwDOV8tcXc68Tqmu","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 2 (inline, `add_comment_to_pending_review`): one link, URL of 104 characters: [a12.txt](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/a12.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"a12.txt","line":2,"original_line":2,"author":"Nerlant","created_at":"2026-11-26T09:37:31Z","updated_at":"2026-11-26T09:38:10Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706367942"}],"total_count":1},{"id":"PRRT_kwDOV8tcXc68TqzV","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 3 (inline, `add_comment_to_pending_review`): one link, URL of 149 characters: [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt","line":2,"original_line":2,"author":"Nerlant","created_at":"2026-11-26T09:37:39Z","updated_at":"2026-11-26T09:38:10Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706369378"}],"total_count":1},{"id":"PRRT_kwDOV8tcXc68Tq4i","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 4 (inline, `add_comment_to_pending_review`): one link, URL of 150 characters: [url-150](`` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ``)\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt","line":2,"original_line":2,"author":"Nerlant","created_at":"2026-11-26T09:37:42Z","updated_at":"2026-11-26T09:38:10Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706369973"}],"total_count":1},{"id":"PRRT_kwDOV8tcXc68TrA3","is_resolved":false,"is_outdated":false,"is_collapsed":false,"comments":[{"body":"Case 5 (inline, multi-line, `add_comment_to_pending_review`): three links of 150, 151 and 152 characters in one body, in the sentence shapes of the original review: first ([url-150](`` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ``)), then ([url-151](``` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-151-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ```)). Last ([url-152](```` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-152-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ````)), end.\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","path":"fixtures/url-151-chars-xxxxxxxxxxxxxxxxxxxxxxxxxxx.txt","line":3,"original_line":3,"start_line":1,"original_start_line":1,"author":"Nerlant","created_at":"2026-11-26T09:37:46Z","updated_at":"2026-11-26T09:38:10Z","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#discussion_r4706370850"}],"total_count":1}],"totalCount":5,"pageInfo":{"hasNextPage":false,"hasPreviousPage":false,"startCursor":"Y3Vyc29yOnYyOpK0MjAyNi0xMS0yNlQwOTozNzoyOVrOvE6ouQ==","endCursor":"Y3Vyc29yOnYyOpK0MjAyNi0xMS0yNlQwOTozNzo0NlrOvE6wNw=="}}
~~~~~

## 19. `mcp__github__pull_request_read`

- Called: 2026-11-26T09:38:18.900404Z
- Returned: 2026-11-26T09:38:19.740263Z

Input:

~~~~~json
{
  "method": "get_comments",
  "owner": "Nerlant",
  "pullNumber": 1,
  "repo": "review-link-repro"
}
~~~~~

Output:

~~~~~text
[{"id":7188260651,"body":"Case 7 (PR conversation comment, `add_issue_comment`): [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3) and [url-150](`` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ``).\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","html_url":"https://github.com/Nerlant/review-link-repro/pull/1#issuecomment-7188260651","user":{"login":"Nerlant","id":17651174,"profile_url":"https://github.com/Nerlant","avatar_url":"https://avatars.githubusercontent.com/u/17651174?v=4"},"author_association":"OWNER","reactions":{"total_count":0,"+1":0,"-1":0,"laugh":0,"confused":0,"heart":0,"hooray":0,"rocket":0,"eyes":0},"created_at":"2026-11-26T09:38:12Z","updated_at":"2026-11-26T09:38:12Z"}]
~~~~~

## 20. `Bash`: independent read of the PR body via the REST search API (gh api)

- Called: 2026-11-26T09:38:19.375861Z
- Returned: 2026-11-26T09:38:21.711818Z

Input:

~~~~~json
{
  "command": "gh api -X GET search/issues -f q='repo:Nerlant/review-link-repro is:pull-request' --jq '.items[] | {number, html_url, body}'",
  "description": "Read PR body via GitHub search API, bypassing MCP"
}
~~~~~

Output:

~~~~~text
{"body":"Case 0 (PR description, `create_pull_request`): [url-149](https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-149-chars-xxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3) and [url-150](`` https://github.com/Nerlant/review-link-repro/blob/6e7bdc80e2a244654c297b34eb5e593ebf8c2aef/fixtures/url-150-chars-xxxxxxxxxxxxxxxxxxxxxxxxxx.txt#L1-L3 ``).\n\nReproduction of Markdown links being rewritten when a Claude Code cloud session posts to GitHub through the GitHub MCP tools. The review on this PR holds cases 1 to 6 and the conversation comment holds case 7. Each case states the length of its URLs.\n\n---\n_Generated by [Claude Code](https://claude.ai/code)_","html_url":"https://github.com/Nerlant/review-link-repro/pull/1","number":1}
~~~~~
