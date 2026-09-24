# Git in Codespaces: updates, pull, and conflicts

When nothing is wrong: protect your work (**Commit → Push**), then **Sync fork → Pull**.

**Save** writes the file on disk. **Commit** stores a snapshot in Git.  
**Diverge** = each side has commits the other does not.  
**Conflict** = same lines of the same file changed two ways. Different problems.

---

## 1. Happy path — Talia added a new week

You usually start on **github.com** (your fork) because you want the new week. **Before** you click **Sync fork**, make sure any work still only in Codespace is saved into Git and uploaded:

1. Open your **Codespace** → **Commit** (Source Control).
2. **Push** to your fork.
3. Back on **github.com** → your fork → **Sync fork**.
4. Back in Codespace → **Pull**.

After Sync fork, **Pull before you Push** again — pushing from an old Codespace can upload the old copy and undo the sync.  
If Source Control says **Can't push … Try running "Pull" first** → Pull, then Push. Do not force-push.

---



## 2. Sync fork gets stuck

Same story as `git_conflicts.md` (short version).

**Example:** you **committed and pushed** week 3 work → your fork has commits Talia’s repo does not. Talia adds week 4 → her repo has commits you do not. Histories are not one straight line → Sync fork cannot quietly one-click update. You may see **Discard … commits**, plus **Update branch** or **Open a pull request**.


| Button                  | What to do                                                                            |
| ----------------------- | ------------------------------------------------------------------------------------- |
| **Update branch**       | **Try first** to keep your work and take Talia’s updates. Then **Pull** in Codespace. |
| **Open a pull request** | **Skip** — do not open a PR into Talia’s course repo.                                 |
| **Discard … commits**   | Only if you accept losing that work on the fork.                                      |


After Update branch, old week folders may show a **Merge …** commit message. That means histories were joined, not that week01 was rewritten.

If Update branch fails or you are unsure → Codespace below.

### Codespace fix

Download Talia’s latest files, fix a real file conflict only if Git reports one, then **Push**.

```bash
git remote add upstream https://github.com/taliawu17/MATH7810.git
# skip that line if upstream already exists
# (upstream = nickname for Talia’s course repo)

git fetch upstream          # download Talia’s new commits into Git’s records (files on screen unchanged yet)
git merge upstream/main     # apply those commits to the files you see now
```

Then **Push**. If you hit a file conflict or “would be overwritten,” see section 3.

---



## 3. Stops you may hit (pull or merge)

These can appear when you **Pull**, or when you run `git merge upstream/main`. They are different.

### You edited a file but did not Commit yet

```text
error: Your local changes ... would be overwritten by merge
```

(or “commit or stash them”)

**Plain words:** you hit **Save**, but never **Commit**. Git refuses so it does not wipe those edits. This is **not** a file conflict.

**Do one:** **Commit**, then try again · **or** **Discard** that file’s changes in Source Control, then try again.

### Real conflict inside a file (Stop B)

```text
CONFLICT (content): Merge conflict in ...
Automatic merge failed; fix conflicts and then commit the result.
```

The file name in Source Control is often **red**. Two columns show the difference (e.g. `3` vs `2`).

Read the **pane labels**, not “left = Talia” by habit:


| Label                     | Usually means                            |
| ------------------------- | ---------------------------------------- |
| **Current** (often right) | What was already in this Codespace       |
| **Incoming** (often left) | What is coming in (often Talia’s update) |
| **Result** (bottom)       | What will be **saved**                   |


There is **no** silent “always take left” or “always take right.” Choose **Accept Current / Incoming / Both**, or edit Result. For labs, prefer **Incoming** if you only need new materials; keep **Current** only if your work must stay (copy to a new filename first if unsure).

Then **Continue** or **Commit** once → **Push**.

For a **terminal** fix instead (keep a whole file as yours or Talia’s with `git checkout --ours` / `--theirs`), see section 4 of `MATH7810_branch_workflow copy.md`.

New week **folders** rarely conflict with edits in an old notebook.

---



## 4. Last resort — new Codespace

1. Copy out anything you still need (**unpushed** work will be lost).
2. Sync fork → **Discard … commits**.
3. New Codespace from the updated fork.

Prefer Update branch or section 2–3 if you must keep your commits.

---



## 5. Less common: Stop A when you `git pull`

This message is from `git pull`, not from `git fetch` + `git merge upstream/main`. Many students never see it if they use the Codespace steps in section 2.

```text
fatal: Need to specify how to reconcile divergent branches.
```

**Meaning:** Codespace and the remote each have commits the other does not; Git wants merge (this course) or rebase. It has **not** started editing your cells yet.

```bash
git config pull.rebase false
git pull
```

(Or one-off: `git pull --no-rebase`.)

After that you may still hit “would be overwritten” or Stop B (section 3).