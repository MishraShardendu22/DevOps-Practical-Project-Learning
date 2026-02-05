# 🌿 Git Working - Under the Hood

Deep dive into Git's internal architecture and how version control actually works.

---

## 📚 Contents

### [Git Internals](Git-Working.md)

Understanding Git's core concepts and architecture.

---

## 🎯 Core Concepts

### Content-Addressable Storage

Git is fundamentally a **key-value store**:
- **Key**: SHA-1 hash of content
- **Value**: Compressed object data
- All data stored in `.git/objects/`

### Object Types

Git has **four core object types**:

#### 1. **Blob** (Binary Large Object)
- Stores file contents only
- No filename, no metadata
- Pure data storage

#### 2. **Tree**
- Represents a directory
- Maps filenames to blobs or other trees
- Creates directory structure

#### 3. **Commit**
- Snapshot of entire project
- Points to root tree
- References parent commit(s)
- Contains author, message, timestamp

#### 4. **Tag** (not detailed in notes)
- Named reference to specific commit
- Used for releases/versions

---

## 🔍 How It Works

### Storage Mechanism

```
Project File → Git Object Database
```

When you stage a file:
1. Git compresses the content
2. Generates SHA-1 hash
3. Stores in `.git/objects/`

### Snapshots vs Deltas

Git stores **snapshots**, not differences:
- Each commit = full snapshot of project
- Unchanged files reuse existing objects
- Efficient through deduplication

### Example Structure

```
Commit A
  └─ Tree (root)
      ├─ blob (file1.txt)
      ├─ blob (file2.txt)
      └─ Tree (subdir)
          └─ blob (file3.txt)
```

When `file1.txt` changes:
- New blob created for file1.txt
- New tree created for root
- Old blobs/trees reused (efficient!)

---

## 🌳 Branches Explained

### What is a Branch?

A branch is simply a **mutable pointer** to a commit:

```
main → [Commit C] → [Commit B] → [Commit A]
```

On new commit:
```
main → [Commit D] → [Commit C] → [Commit B] → [Commit A]
```

### Branch Operations

- **Create branch**: Create new pointer
- **Switch branch**: Move HEAD pointer
- **Merge branch**: Combine histories

---

## 🔀 Merging Strategies

### Three-Way Merge

When branches diverge, Git uses:
1. **Common ancestor** commit
2. **Branch A** tip
3. **Branch B** tip

Git compares all three to determine changes.

### Fast-Forward Merge

When no divergence exists:
- Simply move branch pointer forward
- No merge commit needed

---

## 💡 Key Insights

### Hashes & Immutability

- **Object ID = Hash of content**
- Change content → New ID
- **Commits are immutable**
- History cannot be altered (only rewritten)

### Efficiency Features

1. **Deduplication**
   - Same content = same hash
   - Stored only once

2. **Compression**
   - All objects compressed
   - Minimal disk usage

3. **Delta compression**
   - Pack files use deltas for similar objects
   - Further space savings

---

## 🛠️ Practical Understanding

### View Git Objects

```bash
# List all objects
find .git/objects -type f

# View commit object
git cat-file -p <commit-hash>

# View tree object
git cat-file -p <tree-hash>

# View blob object
git cat-file -p <blob-hash>
```

### Inspect Repository

```bash
# Show commit tree
git log --graph --oneline --all

# Show object type
git cat-file -t <hash>

# Show object size
git cat-file -s <hash>
```

---

## 📊 Mental Model

### Git Workflow

```
Working Directory
    ↓ (git add)
Staging Area
    ↓ (git commit)
Repository (.git)
    ↓ (git push)
Remote Repository
```

### Object Relationships

```
Commit → Tree → Blob
         Tree → Tree → Blob
```

---

## 🎓 Learning Outcomes

After understanding Git internals:

- ✅ Know why Git is fast and efficient
- ✅ Understand how branches really work
- ✅ Comprehend merge strategies
- ✅ Debug Git issues more effectively
- ✅ Use advanced Git commands confidently

---

## 📚 Advanced Topics

### Topics to Explore Next

1. **Git Plumbing Commands**
   - Low-level Git operations
   - Direct object manipulation

2. **Reflog**
   - Reference logs
   - Recovering lost commits

3. **Rebase vs Merge**
   - When to use each
   - History manipulation

4. **Git Hooks**
   - Automated workflows
   - Custom validations

---

## 📖 Related Resources

- [Git Commands](Git-Working.md)
- [Git Documentation](https://git-scm.com/doc)
- [Pro Git Book](https://git-scm.com/book)

---

**Master the internals, master Git! 🚀**
