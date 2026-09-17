# Git in Codespaces: updates, `git pull`, and conflicts

## 1. When the teacher adds a new week

Save your work **before** you take in new files.

1. **Commit** in Codespace (Source Control → message → Commit).
2. **Push** to **your** GitHub fork.
3. On github.com, open **your fork** → **Sync fork** (teacher’s files land on GitHub first).
4. Back in Codespace: `git pull` (or the **down** arrow on the sync button).

After Sync fork, GitHub already has the new week. Pushing from an old Codespace can send the old copy back up and undo the sync.

If Sync fork **cannot** finish (only **Discard … commits** or **Open pull request**), do **not** discard yet. Merge the teacher’s updates in Codespace instead (section 4). If that is too messy, use the last-resort reset (section 5).

Uncommitted files are **not** a merge conflict. If you `git pull` with **uncommitted** edits (saved on disk but not committed), Git often refuses (`commit or stash first`, or “would be overwritten by merge”). Commit (or stash) first — see **dirty working tree** in section 3.

Source Control may say **Can't push refs to remote. Try running "Pull" first.** That means GitHub already has commits this Codespace does not. **Pull first** (merge), fix Stop A / dirty working tree / Stop B if they appear, **then** Push. Do not force-push.

---

## 2. The sync button (↑ / ↓)

In Source Control, next to Commit, the button is a **sync** symbol (circular arrows), not a cloud. It compares this Codespace with **your fork**.

“Ahead” here does **not** mean a newer file timestamp. It means **one place has commits (saved snapshots) that the other place does not**.


| What you see         | Meaning                                                                                                                                                                                      | What to do                                                                  |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------- |
| **↓ only**           | **GitHub has updates this Codespace has not downloaded yet.** Typical: you clicked **Sync fork** (the teacher’s new week is on GitHub first), or you uploaded / edited a file on github.com. | **Pull** (download into Codespace)                                          |
| **↑ only**           | **This Codespace has commits GitHub does not.** You saved work here and have not uploaded it. GitHub still has the older copy.                                                               | **Push** (upload to your fork)                                              |
| **↑ and ↓ together** | **Both sides have commits the other does not.** Neither copy is simply “the new one”; they have forked. Same situation as “divergent branches.”                                              | **Pull first** (merge), fix a file conflict if Git lists one, then **Push** |


---

## 3. Stops you may hit when pulling

They often happen **one after the other**. They are not the same stop.

Typical order: **Stop A** (how to combine histories) → **dirty working tree** (uncommitted edits block the merge) → **Stop B** (same lines clash inside a file). You may see only some of these.

### Stop A — terminal only: divergent branches

```text
hint: You have divergent branches and need to specify how to reconcile them.
...
git config pull.rebase false     # merge
git config pull.rebase true      # rebase
git config pull.ff-only          # fast-forward only
fatal: Need to specify how to reconcile divergent branches.
```

**What it means:** This Codespace and the remote it is pulling from each have commits the other does not. Git **has not combined any files yet**. It will ask you to decide whether to **merge or rebase**.

A typical classroom pattern (student fork):


| Place              | What it has                                                                                                                         |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------- |
| Codespace          | A commit your fork does not have yet (you **Committed** but did **not Push**)                                                       |
| GitHub (your fork) | A commit this Codespace does not have yet (e.g. you edited **another** file on GitHub, or Sync fork already landed teacher commits) |


Both sides moved forward, but **not on the same line** → the histories have **diverged**.

Then `git pull` wants to download the new commits and combine them with yours. Git can combine in two ways (**merge** or **rebase**). If no default is set, it stops with the message above. That is **Stop A** — not a file conflict yet.

- **Merge:** both sides’ commits stay; histories join. Easier in class.  
- **Rebase:** your Codespace commits are replayed on top of GitHub’s latest commit (one straight line). Conflicts during rebase are harder (`git rebase --continue`). Do not use this unless you already know it.

**What to type (this course: merge):**

```bash
git config pull.rebase false
git pull
```

One-off without changing config: `git pull --no-rebase`.

`git config pull.rebase false` means: later `git pull` commands in **this repo** should **merge** (keep both histories and add a merge commit). It does **not** guarantee the merge will succeed. Uncommitted edits on a file the remote also changes → dirty working tree (next). Different files / different regions usually combine automatically. Same committed spot → Stop B.

### Between A and B — dirty working tree (uncommitted edits)

After you clear Stop A and run `git pull` again, the terminal may show:

```text
error: Your local changes to the following files would be overwritten by merge:
        week01/some_notebook.ipynb
error: The following untracked working tree files would be overwritten by merge:
        ...
Aborting
Merge with strategy ort failed.
```

(Wording varies slightly: “commit or stash them” is the same idea.)

**What it means:** Stop A is done — Git is ready to **merge**. It has **not** started combining file contents yet. You still have **saved but uncommitted** edits (or an untracked file) on a path the incoming merge would replace. Git refuses so it does not overwrite your work on disk. That is a **dirty working tree**, not Stop B.

**What to do (pick one):**

1. **Keep the notebook edits:** Commit them in Source Control (message → Commit), then `git pull` again. If the remote also changed the **same** cells, you may then hit Stop B.
2. **Do not need those local edits:** Discard changes to that file in Source Control, then `git pull` again.
3. **Park edits temporarily:** `git stash`, then `git pull`, then `git stash pop` if you still want them (that pop can also lead to Stop B).

Commit (or stash / discard) **before** you expect a clean pull. Uncommitted files are **not** a merge conflict.

### Stop B — inside a file:

Git **already started** combining. The **same lines** of the same file were changed in two ways.

In the **terminal** it often looks like:

```text
CONFLICT (content): Merge conflict in week01/some_notebook.ipynb
Automatic merge failed; fix conflicts and then commit the result.
```

For notebooks, Codespace may open a **two-pane** comparison labelled **Input changed**. 

Stop B is likely if:

1. You already **committed** (push is **not** required for a local merge) a different copy of the **same** `.ipynb`, and
2. The teacher later **committed and pushed** changes to that **same** notebook in the course repo, and
3. You bring those teacher commits in with `git merge upstream/main` (section 4), and Git cannot combine the **same cells / same lines**.
  (If Sync fork had succeeded with no conflict, you would only `git pull` from your fork — that is the easy case in section 1.)

For a lab notebook: keep the **teacher’s** cells if you only need the new materials; keep **yours** only if that work must not be overwritten (copy your work to a new filename first if needed).

#### If Stop B appears in Codespace (usual fix in this course)

1. Read the **Result** (or open the `.ipynb` as a normal notebook) and check the conflicted cells. Choose using the **cell text**. You may only see **Input changed**. After you are happy with the cells, save.
2. Source Control often already shows a merge message such as `Merge remote-tracking branch 'upstream/main'`. Files may be staged with a small **M**.
  - Click **Continue** or **Commit** **once** (whichever the UI shows) to finish the merge commit.
3. **Push** to your fork (↑ or Sync Changes).

```bash
git status   # show whether this Codespace is ahead, behind, diverged, or clean vs your fork
```

Should be clean (not “merging” / not “unmerged paths”).

That is **not** Stop B:

- Uncommitted / would-be-overwritten edits → **dirty working tree** (section above), not Stop B.  
- **Sync fork** may refuse with only **Discard N commits** / **Open pull request**, and **no** conflict editor. That is still a conflict, but you fix it in Codespace (section 4), not by discarding.

**No Stop B:** Git can merge automatically (different files, or different cells that do not overlap). Same name + different content does **not** always cause a conflict.

---

## 4. Sync fork has no “Resolve conflicts”

After you **pushed** your own work, **Sync fork** on github.com may not open a conflict editor. You may only see:


| Button                | Meaning                                                                                                 |
| --------------------- | ------------------------------------------------------------------------------------------------------- |
| **Discard N commits** | Throw away the extra commits on **your fork** (not recommended)                                         |
| **Open pull request** | GitHub’s way to merge the teacher’s commits **into your fork**. **Skip** — use Codespace below instead. |


**Do not** open a pull request that asks to put **your** work into the **teacher’s** course repository.

**Do this in your fork’s Codespace instead**:

```bash
git remote -v   # list remotes (origin = your fork; check whether upstream already exists)
```

If there is no `upstream` yet, add the **course** repo (not your fork):

```bash
git remote add upstream https://github.com/taliawu17/MATH7810.git
```

Then:

```bash
git fetch upstream   # download the teacher’s latest commits (does not change your files yet)
git merge upstream/main
```

If the default branch is `master`, use `upstream/master`. If `git branch -a` shows no `remotes/upstream/main`, the remote URL or branch name is wrong.

That merge is what pulls the teacher’s new week (and any edits the teacher pushed to **old** notebooks) into **your** branch. Same-file overlap → Stop B (section 3). When the merge commit is done, **Push** so your fork matches this Codespace.

If you already finished the merge **only** on GitHub (Resolve conflicts, or a PR **on your fork**), then in Codespace `git pull` is often a fast-forward with **no** new Stop B — unless this Codespace still has extra **unpushed** commits on the same file.

---

## 5. Quick reset: new Codespace (last resort)

If the merge UI is stuck and you only need the teacher’s latest files (and can redo or re-paste your own work):

1. **Copy out** anything you still need from the old Codespace (download the notebook, or paste cells elsewhere). **Unpushed** work will not survive a reset.
2. On **your fork** on github.com → **Sync fork** → **Discard N commits** (this makes your fork match the course repo; your fork’s extra commits on that branch are gone).
3. Delete or stop the old Codespace.
4. **Create a new Codespace** from **your fork**.

The new Codespace starts from the updated fork — usually **no** Stop A / dirty working tree / Stop B leftover from the old session.

This is **not** the usual fix. Prefer section 3 or 4 if you must keep your commits. Only use Discard + new Codespace when you accept losing that fork history.