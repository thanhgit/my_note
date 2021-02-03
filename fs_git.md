# Git filesystem

## How to use `git mv`
- Change name of file. This is good practice
```bash
git mv git.md local_git.md
```
- Using `--force` flag for filename already exists, then Git overwrite whatever file is at the destination
```bash
git mv --force git.md local_git.md
```

## How to use `git rm`
- Removing version-controlled files is also safer
- Removing directory and all unignored files under it
```bash
git rm -r --dry-run src
```

## How to use `git reset`
- Resetting files to the last commit
    - `--hard` flag: to reset both the index staging area and the working directory to state of the previous commit on this branch, undo `git add` and all file modifications
    - `--mixed` flag (default): reset the index staging area but not the contents of the working directory, only undo `git add`
    - 
```bash
git reset --hard
---
HEAD is now at 06b5eb5 Remove unfavourable review file
```

## Deleting ignored files
- Removing only ignored files from the working directory
```bash
git clean --force -X
```
- Removing ignored files and all the untracked files
```bash
git clean -x
```
- Often `git clean -xdf` in run after `git reset --hard` to reset all files of last-commit and remove all uncommited files

## How to use `git stash`
- The stashes are stored on a stack structure
- You want to undo current changes but redo them at a later point
```bash
git stash save
---
Saved working directory and index state WIP on master: fda1b1e change README
```
- Show temporary commit
```bash
git stash list
---
stash@{0}: WIP on master: fda1b1e change README
```
- Only once, get temporary commit, It will apply to the working directory and removed from the stack
```bash
git stash pop
```
- Multiple times, get temporary commit
```bash
git stash apply
```
- Clear stash stack
```bash
git stash clear
```

## Assuming files are unchanged and no-unchanged
```bash
git update-index --assume-unchanged demo.txt
git ls-files -v
git update-index --assume-unchanged demo.txt
```


