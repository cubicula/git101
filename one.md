# Setup a repo
* Create and go to your project folder

`git init`

`git remote add origin https://github.com/cubicula/git101.git` # setup remote repo

`git pull origin master` # ???

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