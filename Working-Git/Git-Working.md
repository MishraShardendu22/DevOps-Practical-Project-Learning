# How Git Works Under the Hood ?

* Git is a **content-addressable key–value store** (object database).

  * Key = SHA-1 hash of content
  * Value = compressed object

* All data lives in `.git/objects/`.

* **Core object types**

1. **Blob**

   * File contents only (no filename, no metadata).

2. **Tree**

   * Represents a directory.
   * Maps filenames → blobs or other trees.

3. **Commit**

   * Snapshot of entire project.
   * Stores: root tree, parent commit(s), author, message, timestamp.

* **Snapshots & Storage**

* When a file changes, only objects on the path from that file to root tree change.
* Unchanged subtrees are reused → efficient storage.

* **Hashes & Immutability**

* Object ID depends on content.
* Change content → new ID.
* Commits are immutable.

* **Branches**

* A branch is just a **mutable pointer to a commit**.
* On each commit, the current branch pointer moves forward.

---

* **Merging**

* If two branches diverge, Git performs a **three-way merge**:

  * Common ancestor
  * Branch A tip
  * Branch B tip

Cases:

* Different files or different lines → auto merge.

* Same line modified → merge conflict → user resolves.

* Merge produces a **new commit with two parents**.

---

* **Rebase**

* Alternative to merge that keeps history linear.
* Finds commits unique to feature branch.
* Re-applies them one by one on top of target branch.
* No merge commit created.

---

* **Cherry-pick (internal idea behind rebase)**

* Take changes introduced by a single commit.
* Compute diff between commit and its parent.
* Apply that diff onto another commit using a three-way-merge-like strategy:

  * Base = parent
  * From = commit
  * To = target commit
* Conflicts appear if overlapping changes.

---

* **Big Picture**

* Git = snapshots, not diffs.
* Objects: blobs, trees, commits.
* Branches = names for commits.
* Merge = combine histories.
* Rebase = replay commits.
