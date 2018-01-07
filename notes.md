
# Notes on understanding basic git concepts

#### Delete a branch
`git branch -d <branch_name>`

Notice it doesn't delete the commits of that branch, just the pointer.

## reset
Which banch will this command move?
`git reset HEAD^`

Answer: the branch on which we are before this command is executed.

E.g., we are on master, so master will move one commit back.

## Comparing _checkout_ and _reset_
Both _checkout_ and _reset_ do move HEAD.

_checkout_ *doesn't* moves branch pointer, it just moves HEAD to it

...

_reset_ *does* move branch pointer

Note: if `-- <file>` is added to either of the commands, they don't move HEAD. That's how the commands are similar.

## Moving a branch
I've come across this command 
```
git branch -f master HEAD^3
```
which moves master to a commit from its past.

Why not use `git reset`? How is it different?

(Maybe you don't have to be _on_ the branch you want to move.)

## Committing
#### Remove a file from staging area
`git reset <filename>`

? how do I reconcile this with the statement that 
>It also is used to copy files from the history to the stage without touching the working directory.

If I add file A to staging and then I want to unadd it, I'm supposed to `git reset A`.

But this supposedly also copies file A from HEAD to staging, so now it _is_ in staging.

So how can it be removed from staging if it is added to the staging...

Maybe a hint:

Say _file1_ was modified. `git diff` will show the change because _file1_ has not been added to the staging area. After `git add file1`, `git diff` will stop showing the change, because the files are the same in working and staging.

```
D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ cat test1.txt
test1
new line in test1.txt
another new line in test1.txt
yet another line

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git diff -- test1.txt
warning: LF will be replaced by CRLF in test1.txt.
The file will have its original line endings in your working directory.
diff --git a/test1.txt b/test1.txt
index 53ef2f0..b3a1fe2 100644
--- a/test1.txt
+++ b/test1.txt
@@ -1,3 +1,4 @@
 test1
 new line in test1.txt
 another new line in test1.txt
+yet another line

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git status
On branch master
Changes not staged for commit:
        modified:   one.md
        modified:   test1.txt
        modified:   test2.txt
no changes added to commit (use "git add" and/or "git commit -a")

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git add test1.txt

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git status
On branch master
Changes to be committed:

        modified:   test1.txt

Changes not staged for commit:

        modified:   one.md
        modified:   test2.txt

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git diff -- test1.txt

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$
```

That probably says that _file1_ *was* in staging before was "added". So I don't understand what "added" means.

If we now unstage _test1.txt_, the change will show up again. Why? Because `git reset` does copy files from _HEAD_ to staging!

```
$ git reset -- test1.txt
Unstaged changes after reset:
M       one.md
M       test1.txt
M       test2.txt

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git diff -- test1.txt
diff --git a/test1.txt b/test1.txt
index 53ef2f0..b3a1fe2 100644
--- a/test1.txt
+++ b/test1.txt
@@ -1,3 +1,4 @@
 test1
 new line in test1.txt
 another new line in test1.txt
+yet another line
```

There's a huge difference between `git reset <file>` and `git reset <ref>` !

Well, maybe not that much. Defaults to HEAD.

Ok, little more understanding of the staging area:
* it has the exact copy of the repo, HEAD
* until something is added to it by `git add`
* the _add_ is not really an add, it's copy
* so a file gets rewritten from its repo version to its working area version
* git sees it's modified so it says "I will add it to the next commit"
* if it's not modified then there's nothing to commit
* removing a file from a staging area means that git does't see any changes between staging and repo - to do that, files must be synced from repo to staging and this is what `git reset` does
* key take-away: git decides what to commit based on the difference between STAGING and REPO.
*Not* based on the difference between WORKING-STAGING.

---
##### working-repo
`git diff HEAD` ... is working area different from repo? Run this at the end of the sequence to see nothing is returned and they are in sync.

_Will this show differences for all files or just the ones that are tracked?_

No, `git diff HEAD` will show differences only for tracked files, or maybe for just the files that _are_ in the HEAD! (So this command doesn't care about the staging area at all.)

So upon adding a file _test2.txt_ and not adding it to staging:

```
Untracked files:
  (use "git add <file>..." to include in what will be committed)

        test2.txt

```

And after committing some other files in the project, this is the output of `git show HEAD:test2.txt`:

```
$ git show HEAD:test2.txt
fatal: Path 'test2.txt' exists on disk, but not in 'HEAD'.
```	

And `git diff HEAD` doesn't show anything:

`$ git diff HEAD`

What if _test2.txt_ was already committed once, but isn't staged? Then I think `git show HEAD:test2.txt` will just show the latest committed version and `git diff HEAD -- test2.txt` will show the difference between the working copy of the file and the last commited version.

Correct.

```
$ git status
On branch master
Changes to be committed:
        modified:   test1.txt

Changes not staged for commit:
        modified:   test2.txt


D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git show HEAD:test1.txt
test1
new line in test1.txt
another new line in test1.txt

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git show HEAD:test2.txt
line 1

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git diff
warning: LF will be replaced by CRLF in test2.txt.
The file will have its original line endings in your working directory.
diff --git a/test2.txt b/test2.txt
index 89b24ec..7bba8c8 100644
--- a/test2.txt
+++ b/test2.txt
@@ -1 +1,2 @@
 line 1
+line 2

D@ux32 MINGW64 /d/Root/David/learnGit (master)
$ git diff HEAD
warning: LF will be replaced by CRLF in test2.txt.
The file will have its original line endings in your working directory.
diff --git a/test2.txt b/test2.txt
index 89b24ec..7bba8c8 100644
--- a/test2.txt
+++ b/test2.txt
@@ -1 +1,2 @@
 line 1
+line 2
```
---

To copy from staging to working, do `git checkout -- <filename>` but I don't know what that would be for.

!!! Another effect of _checkout_: switching branches.

`git checkout dev`, where _dev_ is a branch name, makes _HEAD_ point to _dev_ *AND* also copy files from _dev_ to staging and working.

!!! will it delete files in working directory if the _dev_ branch doesn't have them????

- try this out


## Caveats
### Smuggling a file between branches
1. Create file f in branch B1
2. Checkout banch B2

f appears in the working directory of B2. Why? It wasn't added to B1, so it wasn't committed to B1, so the checkout to B2 doesn't delete it, because it's not tracked by git, git doesn't care about the file.

This may be dangerous of the code depends on the presence of a file.

Always do git status to see untracked files and consider whether they affect the program.

## Tracking, branch tracking

## Committing
Note:
- your index gets committed, not your working tree!!! (This has many implications.)
- you get useful stuff stored with commit - timestam, author, and the whole history - because the new commit has a pointer to its predecessors
- there are other commands apart from `git commit` that _create_ commits - `merge`, `pull`, `push`

## Repo
Note:
- remote repo is a _different_ repo than the local repo even if they are linked. There may be branches in the local repo that aren't in the remote repo.
- Repo is a graph - one graph. Local repo is one graph and remote repo is another graph. They may be the same graphs (I guess) but thay may be different at times, so they are two graphs, hence two repos.

## Branches
Note:
- the most important thing is that they are just pointers to commits
- the second most important thing is that they move with commits
  
## _HEAD_
Note:
- OK, _HEAD_ points to the currently checked-out commit or branch, but what does it _mean_?
	- it means that the working tree _has_ the copy of the tree of the commit _HEAD_ points to - at least initially right after the commit
		(exceptions: some untracked files may be in the working tree that have nothing to do with the commit)
	- it also sets context for git commands, e.g., `git status` shows the status of the branch _HEAD_ points to. Or `git push` pushes the checked-out branch.
	
## Tags
One way to think of tags is that they are pointers that point to the non-leaf (not-the-last-one) commits, unlike branches typically do. 

The most important thing about tags is that they do not move with commits as opposed to branches which do move with commits. So a tag is stuck on a single commit and never moves from that commit. Unless one deletes the tag and creates it elsewhere.

I don't understand how to track changes in tags locations. If a tag marks a release for example, it should be tracked what happened to the tag in the past.
