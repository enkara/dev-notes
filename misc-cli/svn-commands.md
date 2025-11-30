# SVN Commands 🧭

A small collection of useful Subversion (SVN) commands for everyday work.

---

## 📚 References

- [Apache Subversion – Official Documentation](https://subversion.apache.org/docs/)

---

## 🔍 Status & Update
```
bash
# Show status of your working copy
svn status

# Update your working copy to the latest revision
svn update
```
---

## ✏️ Locking & Unlocking Files
```
bash
# Lock a file (prevent others from committing changes)
svn lock FileName

# Unlock a file
svn unlock FileName

# Steal a lock from someone else (use with care!)
svn lock --force FileName
```
---

## 💾 Committing Changes
```
bash
# Commit a single file with a message
svn commit -m "MESSAGE" FileName

# Commit multiple files
svn commit -m "MESSAGE" File1 File2

# Commit everything that is modified/added in the current directory
svn commit -m "MESSAGE"
```
---

## ➕ Adding & ❌ Deleting Files
```
bash
# Add a new file or directory
svn add NewFile.txt
svn add NewFolder

# Delete a file or directory (schedule for deletion)
svn delete OldFile.txt
svn delete OldFolder
```
---

## 🧭 Inspecting History
```
bash
# Show log history for the current path
svn log

# Show log history for a specific file
svn log FileName

# Show detailed info about a file or directory
svn info FileName
```


