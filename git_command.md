# Git commands and common terms.
## Common git command.
* Initialize a local repo.
```
git init
```

* Check the status of all files in the repo.
```
git status
```

* check commits and branches history.
```
git log
    --stat
    -p
    --oneline
    -w
    --decorate  # show tags if any.
    --oneline --decorate --graph --all  # shows actual branching that's happening
```

* add files from the working directory to the staging index.
```
git add
```

* take files from the staging index and save them in the repo.
```
git commit
```

* see changes that have been made but haven't been commited yet. is the same as the "git log -p" command.
```
git diff
```

* add tags to specific commits
```
git tag
    -a <tagname>  # recommended because it will make a annotated tag, instead of a lightweight tag.
    -d <tagname>  # delete tag.
    -a <tagname> <SHA>  # tag a commit that occured further back in the repo history.
```

* branch commands
```
git branch  # list all branch names
    <name>  # create a new branch.
    <name> <SHA>  # create a branch and have it point to the specific commit.
    -d <name>  # delete a branch
    -D <name>  # force deletion. (use with caution as commits on that branch will be lost)
```

* switch between different branches and tags.
```
git checkout
    -b <branch name>  --> create a new branch and switch to that branch.
```

* combines changes on different branches
```
git merge <name-of-branch-to-merge-in>
```

* re-edit the commit message or update the last commit.
git commit --amend

* Download the remote repo via url.
```
git clone <url> <dir_name>
```

## Useful terms.
* .gitignore
* `origin` a variable that contains url for remote repo.
* master
* `head` a pointer indicating the current working commit or branch


