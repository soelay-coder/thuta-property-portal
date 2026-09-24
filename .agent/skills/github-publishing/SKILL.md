---
name: github-publishing
description: Publish this Property Portal workspace and its independent sibling projects as GitHub repositories, with the siblings linked from a root repository as submodules. Use when asked to publish, create, or update these GitHub repositories.
---

# GitHub publishing

Publish the independently versioned project folders in this workspace and make the root repository a cloneable project index whose children are Git submodules.

## Repository map

- `api` → `thuta-api`
- `app` → `thuta-app`
- `mobile` → `thuta-mobile`
- Workspace root → `thuta-property-portal`

Create all four repositories as public when the user requests public publishing. Keep each sibling's existing Git history. The root repository contains the root documentation and Git submodule entries for the three siblings; do not copy sibling source into the root history.

## Workflow

1. Inspect Git status, repository roots, existing remotes, and tracked files for the root and each sibling. Preserve local work and existing remotes; do not discard or rewrite history.
2. Check for a callable GitHub plugin/tool. If none is available, use the authenticated GitHub CLI (`gh`). Discover the signed-in owner with `gh api user --jq .login`; do not guess the account name.
3. Before public creation or push, check that no secrets or local-only artifacts are tracked. Keep `.env` files, credentials, databases, uploads, dependency folders, build outputs, and OS metadata out of public commits. Never print secret contents. Stop and explain any tracked sensitive data that cannot be safely resolved without changing user data.
4. Create missing sibling repositories as public, add or update their `origin` remotes without overwriting unrelated remotes, and push every existing local branch and tag while preserving their commit histories. If an expected repository already exists, inspect its contents and history before deciding whether to push; do not force-push or replace it.
5. Initialize the root repository only if it is not already a Git repository. Keep root-only documents and `.gitmodules` there. Add `api`, `app`, and `mobile` as submodules pointing to their GitHub URLs and current pushed commits. Do not absorb their working trees into the root commit.
6. Create or update the root GitHub repository as public and push the root branch. Verify each remote's visibility and pushed branches, and confirm that the root records the three submodule URLs and commit IDs.

Only publish when the current user request authorizes public repository creation or updates. If authentication, permissions, naming collisions, or repository content make a safe push uncertain, complete independent local preparation and report the exact blocker; never bypass it with force pushes or by making an existing repository public without authorization.
