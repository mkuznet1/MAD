<!--
PR title (copy, replace the date):
    Sync develop into mad-rccl (YYYY-MM-DD)
Add a qualifier only when the sync is not mechanical, e.g.
    Sync develop into mad-rccl (YYYY-MM-DD) - resolves models.json conflict
-->

Routine forward-merge of upstream `develop` into the long-lived `mad-rccl`
branch. No functional changes are authored in this PR; anything beyond the
merge itself must be called out in *Deviations from a plain merge* below.

<!--
Two hard requirements, see "Reproduce this sync" at the bottom:
  * the head branch must be a dedicated sync branch, never `develop` itself;
  * merge with "Squash and merge" - mad-rccl requires linear history.
-->


## Summary

<!--
Generate with GitHub Copilot ("Copilot" -> "Summary") and leave the generated
text in this section. Do not hand-write a changelog; the commit list below is
the source of truth.
-->

## Sync metadata

| Field | Value |
| --- | --- |
| Source branch | `develop` @ `<short-sha>` |
| Target branch | `mad-rccl` @ `<short-sha>` |
| Merge base before sync | `<short-sha>` |
| Previous sync PR | #`<number>` (or _first sync_) |

### Commits pulled in

<!-- Output of: git log --oneline --no-merges mad-rccl..develop -->

```
<paste here>
```

### Deviations from a plain merge

<!--
Conflict resolutions, adaptations of upstream changes to mad-rccl code, or
anything else that is not a fast, mechanical merge. One bullet per file.
-->

- [ ] Merged cleanly, no conflicts, nothing to declare
- Otherwise: `path/to/file` - what conflicted and how it was resolved

## mad-rccl content that must survive every sync

- [ ] `models.json`: `primus_pyt_megatron_lm_train_llama-3.1-{8b,70b,405b}_overlay`
      and `sglang-disagg-deepseek-r1-overlay` entries still present
- [ ] `scripts/primus_megatron-lm/` unchanged (unless upstream touched it)
- [ ] `scripts/sglang_disagg/` RCCL/mori-io additions unchanged
- [ ] `docker/*_overlay.ubuntu.amd.Dockerfile` overlay images unchanged
- [ ] `.claude/skills/mad-slurm-multinode/` unchanged
- [ ] No unexpected deletions: `git diff --diff-filter=D --stat origin/mad-rccl..HEAD` is empty

## Validation

- [ ] `python -c "import json; json.load(open('models.json'))"` passes
- [ ] `git diff --stat origin/develop..HEAD` shows only mad-rccl-owned files
- [ ] Head branch is a dedicated sync branch, not `develop`
- [ ] Will be merged with **Squash and merge**
- [ ] Affected multinode benchmark(s) smoke-tested, or explicitly deferred below

Smoke test / deferral note:

<!-- e.g. "primus_pyt_megatron_lm_train_llama-3.1-8b_overlay, 2 nodes: PASS"
     or  "Deferred: upstream delta touches only vllm/xdit, not exercised by mad-rccl" -->

## Reproduce this sync

`ROCm/MAD` rulesets forbid creating branches (`~ALL` + `creation`, no bypass
actors), so the sync branch lives in a personal fork and the PR is cross-repo.

```bash
git remote add fork git@github.com:<you>/MAD.git   # once
git fetch origin develop mad-rccl
BR=sync/develop-to-mad-rccl-$(date -u +%Y%m%d)
git switch -c "$BR" origin/mad-rccl
git merge --no-ff origin/develop -m "Sync develop into mad-rccl ($(date -u +%Y-%m-%d))"
git push -u fork "$BR"
```

Open the PR against `mad-rccl` with this template:

```
https://github.com/ROCm/MAD/compare/mad-rccl...<you>:MAD:<sync-branch>?expand=1&template=sync-develop-to-mad-rccl.md
```

Never open the PR with `develop` as the head branch. `mad-rccl` carries commits
that `develop` does not, so GitHub offers an *Update branch* button that merges
`mad-rccl` into the head branch - with `develop` as head that would push the
RCCL-only work onto upstream `develop`.

Merge with **Squash and merge**: the `mad-rccl-branch` ruleset requires linear
history, so merge commits are rejected. Consequence to keep in mind - after a
squash `develop` is not an ancestor of `mad-rccl`, so the next sync recomputes
the same file changes; they normally auto-resolve because both sides hold
identical content.
