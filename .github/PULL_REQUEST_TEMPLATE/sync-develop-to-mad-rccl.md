# Sync: `develop` -> `mad-rccl`

Routine forward-merge of upstream `develop` into the long-lived `mad-rccl`
branch. No functional changes are authored in this PR; anything beyond the
merge itself must be called out in *Deviations from a plain merge* below.

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
- [ ] Merge commit only - `mad-rccl` history is never rebased or force-pushed
- [ ] Affected multinode benchmark(s) smoke-tested, or explicitly deferred below

Smoke test / deferral note:

<!-- e.g. "primus_pyt_megatron_lm_train_llama-3.1-8b_overlay, 2 nodes: PASS"
     or  "Deferred: upstream delta touches only vllm/xdit, not exercised by mad-rccl" -->

## Reproduce this sync

```bash
git fetch origin develop mad-rccl
git switch -c sync/develop-to-mad-rccl-$(date -u +%Y%m%d) origin/mad-rccl
git merge --no-ff origin/develop -m "Sync develop into mad-rccl ($(date -u +%Y-%m-%d))"
```

Then open the PR against `mad-rccl` with this template:

```
https://github.com/ROCm/MAD/compare/mad-rccl...<sync-branch>?template=sync-develop-to-mad-rccl.md
```
