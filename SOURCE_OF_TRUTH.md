## Source Of Truth

Canonical skills should live in one upstream repository and be mirrored here.

Recommended sync workflow:

1. Update skills in upstream canonical repo.
2. Mirror into this repo using `git subtree` or a copy-sync script.
3. Publish from this repo to `skills.sh`.

Subtree pattern (example):

```bash
git remote add upstream-skills <UPSTREAM_REPO_URL>
git fetch upstream-skills
git subtree pull --prefix skills upstream-skills <UPSTREAM_BRANCH> --squash
```

Rules:

- Do not edit mirrored skills directly in multiple repos.
- Keep skill folder name and frontmatter `name:` identical.
- Treat this repository as a publish target, not the canonical editor target.
