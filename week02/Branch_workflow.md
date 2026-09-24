# MATH7810 two-branch workflow (`main` + `practice`)

Instructor's repository: [https://github.com/taliawu17/MATH7810](https://github.com/taliawu17/MATH7810)

## When to use which section

| When | Do |
| --- | --- |
| **First time** setup | §0 → §1 (create `practice` once) |
| **Day-to-day** lab work | §5 (and §6 when you stop). Before you edit: confirm you are on **`practice`** (`git status -sb` or the branch name in the status bar). |
| **Talia added a new week** | §2 (update `main`) → §3 (merge into `practice`). Use §4 only if there is a conflict. |

You do **not** run every section every week.

---

## Why the two-branch workflow

On GitHub, **Sync fork** can quietly update your copy in one click **only when your history is simply behind Talia’s** — you are missing her new commits, and you have not pushed any of your own commits that she does not have. Then Sync fork just adds her commits on top (**fast-forward**).

`main` is the default line of history for the MATH 7810 repository (the name on GitHub’s branch button). `upstream` is the nickname your Codespace uses for **Talia’s course repo** (as opposed to `origin`, which is your fork).

As soon as **your own commits** are on your fork’s `main` (usually after commit and push), that `main` and `upstream`**’s** `main` are no longer on one straight line — they have **diverged**. Sync fork then typically offers **Discard commits**, plus **Update branch** or **Open a pull request**, which is where most people get stuck.

**This workflow:** keep practice work on a branch called `practice`, and leave `main` free to receive Talia’s files. Then Sync fork on `main` can keep working, and your work is not discarded.

---

## 0. Before creating a branch

Uncommitted edits (and anything still only on `main`) can be lost later when section 2 resets `main`. If your work is still on `main`, commit and push it here first; after §1, do new work on `practice`.

**With buttons (as in class):**

1. **Source Control**
2. Commit any files marked `M` (write a message when asked)
3. **Push**

**Same thing in the terminal** (`Ctrl` + `` ` `` to open it):

```bash
git status -sb                          # (optional) check branch + changes; -s short, -b branch info

git add -A                              # only if you have changes to save; -A = all files
git commit -m "describe your changes"   # only if you staged changes; -m = message
git push                                # only if you just committed (or are ahead of the fork)
```

If there is nothing to commit, skip `add` / `commit`. `git status -sb` is never required to make Git work — it only helps you see where you are.

---

## 1. Create the branch "practice"

```bash
git checkout -b practice        # create branch "practice" and switch to it
git push -u origin practice     # upload practice to your fork; -u = set tracking (needed for github.com)
git branch -vv                  # (optional) confirm practice shows [origin/practice]
```

(Only after `git push -u origin practice` will the new branch `practice` appear on github.com.)

**Before each lab session:** check that you are on `practice` (`## practice` in `git status -sb`, or the branch name in the Codespace status bar). If you see `## main`, run `git checkout practice` before editing.

---

## 2. Bring updates into `main`

**When to run this section:** not right after you create `practice`. Use it **later**, when Talia has added a new week (or other updates) and you want your fork’s `main` to match her course repo again.

Day-to-day lab work stays on `practice` (section 5). After `main` is updated, go to section 3 to bring those updates into `practice`.

**Prefer Option A (UI).** Use Option B only if Sync fork is stuck or you prefer the terminal.

### Option A — buttons on github.com, then Codespace (preferred)

Open your fork: `https://github.com/<your-username>/MATH7810`, select **`main`**, then **Sync fork**. If asked, discard commits on `main` only — your work should already be on `practice`. If **Update branch** appears, use that.

Back in the Codespace:

```bash
git fetch origin --prune        # download from your fork; --prune is optional (drops stale remote refs)
git checkout main               # switch to main (skip if already on main)
git reset --hard origin/main    # make local main match your fork’s main exactly
```

If you clicked **Update branch** and local `main` was already clean/tracking the fork, you can use this instead of the three lines above:

```bash
git pull                        # (alternative) download from your fork and update files you see
```

Note: once you have clicked **Sync fork**, bring `main` down in the Codespace **before** you press **Push** / **Sync Changes**.

### Option B — in Codespace only (backup)

```bash
git remote add upstream https://github.com/taliawu17/MATH7810.git
# only once, if upstream is missing; skip if it already exists

git fetch upstream              # download Talia’s latest commits into Git’s records (files unchanged yet)
git checkout main               # switch to main (skip if git status -sb already shows ## main)
git reset --hard upstream/main  # make local main match Talia’s main (discards commits only on main)
git push --force-with-lease origin main   # update your fork’s main to match
```

- `git reset --hard upstream/main`: forces your local `main` to point at Talia’s version. **It discards your own commits on** `main` — as long as those changes are already on `practice`, nothing is actually lost.
- `--force-with-lease`: safer forced push so your fork’s `main` matches. Your work stays safe on `practice`.

---

## 3. Bring updates into the branch (practice)

```bash
git checkout practice           # switch to practice (skip if already on practice)
git merge main                  # bring main’s updates into practice
git push                        # upload updated practice to your fork
```

After a successful merge, the new week files are in your **`practice`** working copy. Then keep working on `practice` (section 5).

- If it prints `CONFLICT (content): Merge conflict in ...`, see section 4.
- `git push` after a successful merge keeps github.com in sync; skip only if you intentionally want the update only in Codespace for now.

---

## 4. When you hit a conflict

**When it happens**: you and Talia changed **the same spot in the same file**. Different files or different places usually merge with no conflict.

**Preferred (UI, as in class):** In Source Control, open the **red** conflicted file. Use the two-pane view (**Incoming** / **Current** / **Result**), choose what to keep, save, then **Continue** or **Commit** once → **Push**. Details: `Git_pull_and_conflicts.md` §3 (Stop B).

**Optional (terminal)** — keep a **whole file** as one side (pick **one** of the two checkout lines, not both). First confirm you are on `practice` (`git status -sb`):

```bash
git checkout --ours   <your conflicted file>    # keep practice’s version
# OR
git checkout --theirs <your conflicted file>    # keep Talia’s / incoming version

git add -A                      # mark conflicts resolved
git commit -m "<your message>"  # finish the merge commit
git push                        # upload to your fork
```

`git status -sb` showing `UU <file>` means that file is in conflict; Source Control also lists it.

---

## 5. How to commit new changes

**This is for work on `practice`**, not on `main`. Before you edit: confirm you are on **`practice`**. Then commit and push. (Keep `main` clean for Talia’s updates — section 2.)

Commit with commands or with the UI:

```bash
git checkout practice           # skip if already on practice
git add -A                      # stage all changes
git commit -m "your changes"    # save a snapshot
git push                        # upload to your fork
```

---

## 6. Stop the Codespace

When you are done, click the green **Code** button on your repository page; if the Codespace shows a green dot and **Active**, open the **···** menu to its left and choose **Stop codespace**.

---

Draft: Lin Xitong  
Edited: Talia Wu  

Reference: [https://github.com/taliawu17/MATH7810/blob/main/week02/Git_pull_and_conflicts.md](https://github.com/taliawu17/MATH7810/blob/main/week02/Git_pull_and_conflicts.md)

---

This document was prepared with the assistance of artificial intelligence. All commands have been verified in GitHub Codespaces.
