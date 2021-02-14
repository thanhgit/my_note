# Merging technique

## Create a new branch
```bash
git checkout -b doc/merging_tech
```

## Merging with fast-forward strategy
```bash
git chekout master
git merge doc/merging_tech
```

## Merging with non fast-forward strategy (create merge commit)
```bash
git checkout master
git merge --no-ff doc/merging_tech
```
- A merge commit has two parents:
    - The previous commit on the current branch (master)
    - The previous commit on the incoming branch (doc/merging_tech) 

## A merge commit is never created
```bash
git checkout master
git merge --ff-only doc/merging_tech
```