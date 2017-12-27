
#### Delete a branch
`git branch -d <branch_name>`

Notice it doesn't delete the commits of that branch, just the pointer.

## Comparing _checkout_ and _reset_
Both _checkout_ and _reset_ do move HEAD.

_checkout_ *doesn't* moves branch pointer, it just moves HEAD to it

...

_reset_ *does* move branch pointer

Note: if `-- <file>` is added to either of the commands, they don't move HEAD. That's how the commands are similar.


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
