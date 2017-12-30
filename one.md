# Viewing

## View files in staging area
`git ls-files`

## Show files in repo without _checking_ them out
`git show HEAD:<filename>`

`git show master:<filename>` ... in branch _master_

`git show origin/master:<filename>` ... in remote repo _origin_, branch _master_

## Show commits with _log_

`git log -2` ... last two commits

`git log --stat` ... which files changes and the number of changes in them

`git log --name-status` ... show which files were (A)dded, (M)odified

`git log --pretty=oneline`, `pretty=oneline / short / full / fuller`

`git log  --pretty=oneline --stat -- "*.csv" ".txt"` ... shows commit message and which _csv_ and _txt_ files changed

`git log --since=2.weeks` ... since last 2 weeks

Good combos:

`git log -5 --pretty=oneline --name-status`

`git log -5 --pretty=oneline --stat`

# Setup a repo
* Create and go to your project folder

`git init`

`git remote add origin https://github.com/cubicula/git101.git` # setup remote repo

`git pull origin master` # ???

to-do: What about `git clone`?

`git push -u origin master`

## Delete and stage deleted files
`git rm '*A.txt`

!!! works on subdirs too !!!

## Merge
1. go to the branch _into which_ you want to merge
2. `git merge <branch name to marge from>`

## What's more recent, local files or repo??


Notice it doesn't delete the commits of that branch, just the pointer.

# View changes

### Local files (unstaged or outside git repo)
`git diff --no-index -- <file1> <file2>`

To use a custom diff viewer, use `git difftool ...` instead of `git diff ...`

### See changes between workspace and staging area.
(tentative)
`git diff`

### Show only which files have changed
`git diff --name-only`

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

#### Difference in a single *file* between two *braches*
`git <branch1> <branch2> <filename>`

Ex.: `git diff master dev file1.txt`

## Undoing
#### Remove a file from staging area
`git reset -- <filename>`

Example `git reset octofamily/octodog.txt`

#### Cancel local changes, rewrite them with originals in repo
`git checkout HEAD -- <file>` ... this will copy <file> to index as well as working directory
  
!
This will only copy <file> from index to working directory: 'git checkout -- <file>', so previous _adds_ to index will get copied back to working dir. and you will not get the original.

#### Undo a commit
\# on branch _master_
`git reset HEAD^`

What this does:
1. moves master pointer to one commit back
2. moves HEAD to point there too(or it just keeps pointing at master, I don't know)
3. Copies files from ref to index, but not to working directory
	Tentative: To copy to working directory, do now either `git reset ... --hard' or 'git checkout HEAD'

(Also - `git amend`)

(Also - `git revert`)

## Committing
#### Remove a file from staging area
`git reset <filename>`

Example `git reset octofamily/octodog.txt`

#### Commit with adding all files

`git commit -a -m "<commit message>"`
to-do: Is it really all files? Or just tracked ones? In that case untracked files would not get added with `git commit -a`

#### Correct, amend a commit
`git add <file>` ... this is needed
`git commit --amend -m "<commit message>"`

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

`git checkout HEAD -- octocat.txt`

_(not sure what it does in the staging area)_

## Overwrite local files with last commit

## Recover  deleted local file

## Is everything pushed back to remote repo? (=do I have a backup?)
`git diff origin/master` ... changes between working area and remote repo.

Non-empty result means there are local changes missing from remote repo.

These changes could be already committed locally, so all that is needed is to push them.

Run `git diff HEAD` to see if they need to be committed first.

## Copy file from another branch
From branch Bsource destination branch Bdest:
``git checkout <Bsource> <file_to_copy>``

This really only copies one file and doesn't delete the other files in the destination branch.

*It doesn't switch branch!* 

# Branching
### Create a branch
\# on current commit:
`git branch <branch name>`, `git branch cleanup`
\# on a different commit:
`git branch <branch name> <commit>`, `git branch bugFix HEAD^2^1`
### Switch to branch
`git checkout <branch name>`, `git checkout cleanup`
### Delete a branch
`git branch -d <branch_name>`


## Move branch
1. `git checkout master` ... must be on the branch which is to be moved
2. `git reset <commit>` ... e.g., `git reset HEAD^`

or

(tentative)
`git branch -f master <commit>` ... doesn't require to be first on master

## Tagging
`git tag <tagname> <commit>`

`git describe <commit|branch>` ... the closest past tag, how many hops away

# Remote braches
## Show remote branches
`git ls-remote`

`git remote show origin` ... more detailes about _origin_ remote

### Show tracking
`git branch -vv`

## Setup tracking of remote branch
`git checkout --track origin/serverfix`

or

`git checkout -b <branch> <remote>/<branch>`

