# Setup a repo
* Create and go to your project folder

`git init`

`git remote add origin https://github.com/cubicula/git101.git` # setup remote repo

`git pull origin master` # ???

to-do: What about `git clone`?

`git push -u origin master`

#### Delete a branch
`git branch -d <branch_name>`

Notice it doesn't delete the commits of that branch, just the pointer.

## Changes

### See changes between workspace and staging area.
(tentative)
`git diff`

#### For a specific file only
`git diff <filename>`

### How to get differences between local repo and remote repo?
`git diff <local branch> <remote>/<remote branch>`

`git diff master origin/master`

#### Difference between working area and repository
`git diff`

#### Difference between staging area and <???>
`git diff --staged`

Explained here: http://365git.tumblr.com/post/3464927214/getting-a-diff-between-the-working-tree-and-other

## Committing
#### Commit with adding all files

to-do: Is it really all files?

`git commit -a -m "<commit message>"`

