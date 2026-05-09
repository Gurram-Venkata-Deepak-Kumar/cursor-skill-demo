---
name: commit-and-push-changes-to-git
description: Create a branch, stage changed files, commit with a relevant message, and push to the repository. Use when the user asks to commit and push work, publish changes, or prepare git updates on a branch.
disable-model-invocation: true
---

# commit and push changes to git

## Instructions

Follow this workflow in order.

1. Check repository status.
   - Run `git status` to confirm changed files.
   - Run `git branch --show-current` to confirm current branch.
   - Run `git remote -v` to confirm push destination.

2. Propose branch name and ask confirmation before creating it.
   - If the user gives a branch name, treat it as a draft and confirm it.
   - Otherwise create a descriptive branch name suggestion from the task:
     - `feature/<short-topic>` for new features
     - `fix/<short-topic>` for bug fixes
     - `chore/<short-topic>` for maintenance
   - Ask the user to confirm or edit the branch name.
   - Do not run branch commands until user confirms the final branch name.
   - Commands after confirmation:
     - Create and switch: `git checkout -b <branch-name>`
     - Or switch to existing: `git checkout <branch-name>`

3. Review what will be committed.
   - Run:
     - `git status`
     - `git diff`
     - `git diff --staged`
   - Ensure no secrets are included (`.env`, keys, credentials).
   - Stage only relevant files for the requested work:
     - `git add <file-or-dir>`
     - Use `git add .` only if all changes are intended.

4. Propose commit message and ask confirmation before committing.
   - Message should explain why the change exists, not only what changed.
   - Prefer concise conventional prefixes:
     - `feat: ...`
     - `fix: ...`
     - `chore: ...`
     - `docs: ...`
     - `refactor: ...`
     - `test: ...`
   - Ask the user to confirm or edit the commit message.
   - Do not run `git commit` until user confirms the final message.
   - Commit with a heredoc pattern:
     ```bash
     git commit -m "$(cat <<'EOF'
     <type>: <short summary>

     <optional extra context if needed>
     EOF
     )"
     ```

5. Push branch to remote.
   - First push with upstream:
     - `git push -u <remote> <branch-name>`
   - Later pushes:
     - `git push`

6. Handle common failures.
   - Non-fast-forward push rejected:
     - `git fetch <remote>`
     - Rebase or merge based on team preference, then push again.
   - Pre-commit hook failed:
     - Fix issues, stage updates, create a new commit.
   - Merge/rebase conflicts:
     - Resolve conflicts, stage files, continue rebase/merge, then push.

7. Final verification.
   - Run `git status` and confirm clean working tree.
   - Confirm branch tracks remote and push succeeded.

## Confirmation Rules

- Always pause for user confirmation at two points:
  - Before branch creation/switch (branch name is editable)
  - Before commit (commit message is editable)
- If the user edits branch name or commit message, use the edited value exactly.
- If the user rejects a proposal, regenerate a better proposal and ask again.
- Never proceed to the next git action until confirmation is received.

## Output Template

Return a short report:

- Branch: `<branch-name>`
- Staged files: `<count and key paths>`
- Commit: `<sha> <message>`
- Push target: `<remote>/<branch-name>`
- Result: `success` or `failed` with reason
