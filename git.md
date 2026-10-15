# Working with a forked repo: a reference guide

## 1. The three copies

| Name | Where | Who can change it |
|---|---|---|
| `upstream` | `github.com/rohitg00/ai-engineering-from-scratch` | Only the author |
| `origin` | `github.com/mohammedanasa/ai-engineering-from-scratch`, your fork | You |
| local | the folder on your Mac | You |

Changes always move in this direction: **upstream → your local copy → origin (your fork) → a PR**.

**What you can merge:**
- A PR into **your fork** (`mohammedanasa/...:main`): you can merge it yourself.
- A PR into **upstream** (`rohitg00/...:main`): only the author can merge it. You open it and wait for review.

That second case is what happened with PR #617.

## 2. One-time setup

Your repo already has this. You only need it again on a new clone:

```bash
git clone https://github.com/mohammedanasa/ai-engineering-from-scratch.git
cd ai-engineering-from-scratch
git remote add upstream https://github.com/rohitg00/ai-engineering-from-scratch.git
git remote -v        # check: origin = your fork, upstream = rohitg00
```

## 3. Workflow for each new piece of work

**Step 1: update your `main` before you start.** Doing this every time prevents most conflicts.
```bash
git checkout main
git fetch upstream
git merge upstream/main        # bring in the author's new commits
git push origin main           # update your fork's main on GitHub
```
(Clicking **Sync fork** on GitHub updates your fork's `main` in the same way. Run `git pull origin main` afterwards so your local copy matches.)

**Step 2: create a branch from the updated `main`.**
```bash
git checkout -b phases/06-something
```

**Step 3: work and commit.**
```bash
git status                     # check what changed
git add path/to/files          # add specific files, not everything
git commit -m "feat(phase-06/01): add tokenizer notes"
```

**Step 4: push the branch to your fork.**
```bash
git push -u origin phases/06-something
```

**Step 5: open the PR on GitHub.**
- Click **Compare & pull request**, or click **compare across forks**.
- **Check the base repository first.** GitHub selects `rohitg00/...` by default for forks. Change it to `mohammedanasa/...` if the work is for your fork.
- Create the PR, then click **Merge pull request**.

**Step 6: clean up after merging.**
```bash
git checkout main
git pull origin main
git branch -d phases/06-something
```

## 4. When GitHub says "Can't automatically merge"

This means your branch and `main` changed the same lines of the same file. Fix it on your machine:

```bash
git checkout my-branch
git fetch origin
git merge origin/main
# Git lists the files in conflict ("CONFLICT (content): ... .gitignore")
```

Open each conflicted file. You'll see markers like these:

```text
<<<<<<< HEAD
your branch's lines
=======
main's lines
>>>>>>> origin/main
```

Edit the file so it reads the way you want. Usually that means keeping both sides. Delete the three marker lines, then finish the merge:

```bash
git add .gitignore
git commit --no-edit
git push origin my-branch
```

GitHub updates the open PR, and the Merge button turns green.

In your case, the original author's commits added lines to `.gitignore` and your branch also added lines, so I kept both.

## 5. Habits that keep this smooth

1. **Sync `main` before creating any new branch.** This is step 1 above.
2. **Never commit directly on `main`.** Use a branch for every piece of work.
3. **Keep large or personal files out of git.** Model weights, datasets, PDFs and lock files (`uv.lock`) go in `.gitignore` *before* you run `git add`. Your last PR had +440k lines mainly because of these.
4. **Read the PR header line before clicking Create.** It shows `<base repo>:main ← <your repo>:<branch>`.
5. **Contributing to the original author's repo has extra rules.** They're in `AGENTS.md`: one commit per lesson, a `docs/en.md` and a `quiz.json`, and tests.

## 6. Quick command reference

| Goal | Command |
|---|---|
| See which branch you're on and what changed | `git status` |
| See your remotes | `git remote -v` |
| Update `main` from the original author | `git checkout main && git fetch upstream && git merge upstream/main` |
| Create a new branch | `git checkout -b name` |
| Push a branch for the first time | `git push -u origin name` |
| See commits on your branch that aren't on `main` | `git log --oneline main..name` |
| Test a merge for conflicts without changing anything | `git merge-tree --write-tree origin/main name` |
| Cancel a merge that went wrong | `git merge --abort` |

If you'd like, I can also save this as a Markdown file (for example in your `turtorials/` folder) so you can keep it with your notes.