# History visualization git

## How to use `git log`
- Show commit with the flags
    - `--author`, `--before`, `--after`
    - `--grep`, `--reverse`
    - `--merges` : show merge commits
```bash
git log --author "thanhgit" --after "Mon Feb 1 23:41:17 2021 +0700" --grep "refactor"
```
- Custom format
    - `%ar`: date
    - `%an`: author name
    - `%s`: commit message
```bash
git log --format="%ar %an did: %s"
---
80 minutes ago Nguyễn Phúc Thạnh did: done filesystem git
4 hours ago Nguyễn Phúc Thạnh did: add filesystem operation in git
2 days ago util4dev did: change README
2 days ago util4dev did: refactor
```
- Using `git shortlog` to overview commit
- Using `git log --oneline --graph --decorate`

## How to use `git blame`
- Show the commit, person and date line by line
```bash
git blame --date=short demo.tf
fee3f4ef (nguyen van a             2020-11-05 1) module "demo" {
803360d0 (nguyen van b 2021-02-03 2)   count = (length(var.role_arns) == 0) ? 1 : 0
fee3f4ef (nguyen van a             2020-11-05 3)   source = "./modules/demo"
defebdbc (nguyen van a 2020-12-23 9) }
```

## How to use `git bisect`
- Finding which commit caused a particular bug

## How to use `git merge`