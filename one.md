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

#### Difference between *staging* area and *working area*
`git diff`

#### Difference between HEAD and staging area
`git diff --cached`

#### Difference between HEAD and working area
`git diff HEAD`

Explained here: http://365git.tumblr.com/post/3464927214/getting-a-diff-between-the-working-tree-and-other

## Committing
#### Remove a file from staging area
`git reset <filename>`

#### Commit with adding all files

to-do: Is it really all files?

`git commit -a -m "<commit message>"`

### A commit sequence with checking
Idea - run diff before and after each step to verify it's doing the expected thing

---
##### working-repo
`git diff HEAD` ... is working area different from repo? Run this at the end of the sequence to see nothing is returned and they are in sync.

`Will this show differences for all files or just the ones that are tracked?`

---
##### working-staging
`git status`

`git diff` ... between working and staging, this is to see whether staging has the stuff from working area that we want to commit

`git add <file_to_track>`

`git diff` ... must show nothing, so we know staging has the stuff we want to commit

---
##### staging-repo
!! Careful - if a file has not been added to the staging area, diff will sho nothing, giving the impression working and staging are in sync even though there's a whole file missing

`git diff --staged` ... between staging and repo branch

`git commit -m "<message>"`

`git diff --staged` ... must show nothing, so we know staging has been committed to repo

`git diff HEAD` ... must show nothing, so we know working is in sync with repo

---
##### local-remote

`git diff origin/master` ... between working(??? or staging or local repo???) and remote repo, to see what hasn't been pushed to remote

`git push origin master`

`git diff origin/master` ... must show nothing, so we know remote is synced with local

---
## Redoing things
### Single file roll-back
Go back to the last checked-in version of a single file

`git checkout HEAD -- <filename>` ... rewrite file in the working directory 

_(not sure what it does in the staging area)_

