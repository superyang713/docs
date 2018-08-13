Git is a very popular version control system (VCS). It records changes to a
file or set of files over time so that a specific version can be recalled
later. It can be used locally, however, it works best with Github, a platform
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
   If you don't know what to add yet, just leave it blank.

## Commit the changes.

* Since a new file `.gitignore` is added, the local repo is different from the
  original state. At this point, it is a good time to commit the first change to
  the local repo.  There are two steps involved when committing a change to the
  local repo.

  First, the changed files in the working directory needs to be added to the
  staging area.

  ```
  git add .
  ```

  I normally use `.` so that I can add all changed files in the current
  folder to the staging area. If you want to be more specific, you can
  replace `.` with a specific file name.

* Then, take files from the staging area and save them in the local repo.

  ```
  git commit -m "a message describing the change of the commit"
  ```

  Since it is the first commit, the message I normally put is "init".

## Create a repository in Github.

* Before the local repo can be pushed to Github, we need to create a repo on
  Github that can hold the local repo.

* This process is done on the Github website in a browser. You can follow the
  [official tutorial](https://help.github.com/articles/create-a-repo/) on how
  to create a repo on Github. Please note that when creating a repo, do not
  initialize the repository with a `README` or `.gitignore`. `README` can
  easily be added later, and `.gitignore` has already been added.

## Push the local repo to Github.

* Now that we have created a repo on Github, and there is a local repo existing
  in our local machine, it is time to push the local repo to the remote repo.
  To do that, we need to link remote and local repo together.

  ```
  git remote add origin <remote-repo-link>
  ```

  The argument `origin` can be considered as a variable name that holds the
  following link.

* Next, the local repo can be pushed to the remote Github repo with the
  following command:

  ```
  git push -u origin master
  ```

  The flag `-u` means remember where to push the repo in the future. So next
  time another commit needs to be pushed, we can just use `git push`.

## Working with a branch.

* When working on a project, try not to work on the master branch directly,
  work on a different branch instead. To create a new branch, type the
  following command:

  ```
  git branch <a-meaningful-branch-name>
  git checkout <a-meaningful-branch-name>
  ```

  Although the two command can actually be combined into one:

  ```
  git checkout -b <a-meaningful-branch-name>
  ```

* After the work is done, commit the change, switch to the master branch, merge
  two branches, and push it to the remote.

  ```
  git commit -m "message"
  git checkout master
  git merge
  git push
  ```

## The following are some git commands I use regularly.

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

* see changes that have been made but haven't been commited yet. is the same as
  the `git log -p` command.

  ```
  git diff
  ```

* add tags to specific commits

  ```
  git tag
      -a <tagname>  # add a tag.
      -d <tagname>  # delete a tag.
      -a <tagname> <SHA>  # tag a specific commit.
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
      -b <branch name>  # create a new branch and switch to that branch.
  ```

* combines changes on different branches

  ```
  git merge <name-of-branch-to-merge>
  ```

* re-edit the commit message or update the last commit.

  ```
  git commit --amend
  ```

* Download the remote repo via url.

  ```
  git clone <url> <dir_name>
  ```

## Useful terms.
* `origin` a variable that contains url for remote repo.
* `master` a branch name, normally the main branch.
* `head` a pointer indicating the current working commit or branch
