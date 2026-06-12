# REPOSITORY_BOOTSTRAP.md

This file defines the first action the GitHub Operator must perform on a new repository.

It does not replace `PURIA.md`.

---

## Purpose

Before creating implementation issues, milestones, branches, or phase pull requests, the GitHub Operator must bootstrap repository settings.

This prevents direct commits to `main` and makes the review workflow enforceable by GitHub, not only by agent instructions.

---

## Required order

For every new repository, the GitHub Operator must run this order:

1. inspect repository and permissions
2. configure repository merge settings
3. configure `main` branch protection or ruleset
4. verify settings
5. only then create project tracking and implementation tasks

---

## Required repository settings

The repository must be configured to:

- allow squash merge
- disable merge commits
- disable rebase merge
- delete head branches after merge
- allow pull request branches to be updated from the base branch

Use `gh api`:

```sh
gh api \
  --method PATCH \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO \
  -f allow_squash_merge=true \
  -f allow_merge_commit=false \
  -f allow_rebase_merge=false \
  -f delete_branch_on_merge=true \
  -f allow_update_branch=true \
  -f squash_merge_commit_title=PR_TITLE \
  -f squash_merge_commit_message=PR_BODY
```

Replace `OWNER` and `REPO` with the target repository.

---

## Required branch protection

`main` must require pull requests.

Use either repository rulesets or classic branch protection.

Repository rulesets are preferred when available.

---

## Preferred ruleset approach

Use a repository ruleset targeting the default branch.

```sh
gh api \
  --method POST \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO/rulesets \
  --input - <<'JSON'
{
  "name": "protect-default-branch",
  "target": "branch",
  "enforcement": "active",
  "conditions": {
    "ref_name": {
      "include": ["~DEFAULT_BRANCH"],
      "exclude": []
    }
  },
  "rules": [
    { "type": "deletion" },
    { "type": "non_fast_forward" },
    {
      "type": "pull_request",
      "parameters": {
        "allowed_merge_methods": ["squash"],
        "dismiss_stale_reviews_on_push": true,
        "require_code_owner_review": false,
        "require_last_push_approval": false,
        "required_approving_review_count": 1,
        "required_review_thread_resolution": true
      }
    }
  ],
  "bypass_actors": []
}
JSON
```

---

## Classic branch protection fallback

If rulesets are unavailable, use classic branch protection.

```sh
gh api \
  --method PUT \
  -H "Accept: application/vnd.github+json" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  /repos/OWNER/REPO/branches/main/protection \
  --input - <<'JSON'
{
  "required_status_checks": null,
  "enforce_admins": true,
  "required_pull_request_reviews": {
    "dismiss_stale_reviews": true,
    "require_code_owner_reviews": false,
    "required_approving_review_count": 1,
    "require_last_push_approval": false
  },
  "restrictions": null,
  "required_linear_history": false,
  "allow_force_pushes": false,
  "allow_deletions": false,
  "required_conversation_resolution": true,
  "lock_branch": false,
  "allow_fork_syncing": true
}
JSON
```

If the default branch is not `main`, detect it first:

```sh
gh repo view OWNER/REPO --json defaultBranchRef --jq '.defaultBranchRef.name'
```

Then use the detected branch name.

---

## Verification

After bootstrap, verify repository settings:

```sh
gh repo view OWNER/REPO \
  --json allowMergeCommit,allowRebaseMerge,allowSquashMerge,deleteBranchOnMerge
```

Verify branch protection or ruleset:

```sh
gh api /repos/OWNER/REPO/rulesets
```

or:

```sh
gh api /repos/OWNER/REPO/branches/main/protection
```

---

## Required GitHub Operator behavior

The GitHub Operator must run this as a dry run first unless the human explicitly allows mutation.

The dry run must show:

- target repository
- default branch
- current merge settings
- planned merge setting changes
- current protection/ruleset status
- planned protection/ruleset changes
- exact commands to run

---

## Failure behavior

If the GitHub Operator cannot configure protection:

- stop
- report the reason
- do not create implementation issues yet
- ask the human to grant repository administration permission or configure settings manually

---

## Final rule

No substantial agent implementation should start until repository protection is active or the human explicitly accepts the risk.
