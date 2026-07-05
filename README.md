# claude-rules

My personal working rules for Claude Code, kept here so any session — including
**cloud / remote sessions** that can't see my local `~/.claude/CLAUDE.md` — can
load them from git.

## Use it in a new session

Paste this at the start of a session:

> Read and follow my working rules: fetch `RULES.md` from
> `github.com/tarnos12/claude-rules` (use `gh api` /
> `gh repo view tarnos12/claude-rules` since it's private) and apply it for the
> rest of this session.

Claude will run something like:

```
gh api repos/tarnos12/claude-rules/contents/RULES.md --jq '.content' | base64 -d
```

(A cloud session needs its GitHub auth to have access to this private repo. If
it can't read a private repo, make this repo public — the rules aren't secret.)

## Files

- `RULES.md` — the rules Claude should follow.
