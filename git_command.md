Git is a very popular version control system (VCS). It records changes to a
file or set of files over time so that a specific version can be recalled
later.It cam be used locally, however, it works best with Github, a platform
that can  host all the local git repos remotely. Git can be intimidating at
first, but it is actually not that hard after some practice. I would like to
demonstrate my workflow with Git and Github and see how I can benefit from
them.

## Initialize a local git repository.

* It is really easy to initialize a local repo, just type

    ```
    git init
    ```

    at the top of the project directory. It will automatically create a hidden
    folder called `.git`. This is where all the version history will be stored.

* Now create a file called `.gitignore` in the same folder.

    ```
    vim .gitignore
    ```

    Inside `.gitignore` file, put the file or folder name so that these files
    will be excluded when the repo is uploaded to the remote host like Github.

## Commit the changes

### Since a new file `.gitignore` is added, the local repo is different from the original state. At this point, it is a good time to commit the first change to the local repo.

* There are two steps involved when committing a change to the local repo.
First, the changed files in the working directory needs to be added to the
staging area.

    ```
    git add .
    ```

    I normally use `.` so that I can add all changed files in the current
    folder to the staging area. If you want to be more specific, you can
    replace `.` with a specific file name.




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
    --oneline --decorate --graph --all
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


