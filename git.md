How to undo the last commit?
```
git reset --hard HEAD~1          # undo and destory all changes
git reset --mixed HEAD~1         # undo the commit but keep changes (files unstaged, requires git add .)
git reset --soft HEAD~1          # undo the commit but keep changes and index (files staged)

git revert HEAD # history-safe undoing
```




How to squash all commits in your feature branch into 1 before pushing?
```
git reset --soft origin/master # will put all modifications to stage
git add .
git commit
```

Check current branch and modifications:
```
git status
```

Add file or files in a directory to stage:
```
git add <file>
git add -A .
git add '*.txt'
```

Remove a file or files from the stage. Not revert in working copy:
```
git reset HEAD <file>
git reset --hard HEAD
```
 
Files can be changed back in working copy by using:
```
git checkout -- <file>          # `--` to promise that there are no more opts after `--`
```

Revert file in the current branch using the master version:
```
git checkout master src/main/java/org/optimus/model/view/View.java
```

Command which will not only remove the actual files from disk, but will also stage the removal of the files:
```
git rm '*.txt'
git rm -r folder_of_cats
```

Commit with comment:
```
git commit -m "comment"
git commit --amend //rewrite last commit 
git commit -a //add all unstaged files
```

History:
```
git log // full log records as hash + author + date + message
git log --oneline //show commits as short hash + message
```

Adding remote repo and naming it origin:
```
git remote add origin https://github.com/try-git/try_git.git
```

Pushing <branch_name> to <origin_name> with -u to remember parameters:
```
git push -u origin master
git pull origin master
git push  <REMOTENAME> <LOCALBRANCHNAME>:<REMOTEBRANCHNAME> 
```

How to update master without checkout?
```
git fetch origin master:master          # works only with fast-forward merge
```

Pushes current branch to it's sybling on the remote:
```
git push <REMOTENAME> HEAD
git pull --rebase origin master
```
 `--rebase` option tells Git to move all of commits to the tip of the master branch after synchronising it with the changes from the central repository 

After pull diff most recent commit, which we can refer to using the HEAD pointer:
```
git diff HEAD
```

Run git diff with the `--staged` option to see the changes you just staged:
```
git diff --staged
```

Creating branches:
```
git branch new_branch
git checkout new_branch
git checkout -b new_branch
```

Delete branch:
```
$ git push --delete <remote_name> <branch_name>
$ git branch -d <branch_name>
```

Merging branch1 into current branch:
```
git merge <branch1>
```

Rebasing current branch onto top of the `branch1`. Makes consequent history. Changes in current branch and stays in it
```
git rebase <branch1>
```
 
Checkout a remote branch:
```
git checkout -b test <name of remote>/test
```

How to squash commits in git after they have been pushed?
```
git rebase -i origin/master~4 master # squash 4 commits locally
git push origin +master # force push it to master
```

Setting git editor, e.g. for vs code:
```
git config --global core.editor "code --wait"
```

Open git config in git editor:
```
git config --global -e
```

### Git stash
Sometimes when you go to pull you may have changes you don't want to commit just yet. One option you have, other than commiting, is to stash the changes.
Use the command `git stash` to stash your changes, and `git stash apply` to re-apply your changes after your pull.
```
git stash save "name1"
git stash list
git stash apply <number_of_stash>
```

Check the list of tracked files:
``` 
git ls-tree -r master --name-only
```

How to know parent commit for commit:
```
git log --pretty=%P -n 1 <commit>
```
 
List remote branches:
```
git branch -r | grep "BE-182"
```

Rename git branch locally and remotely:
```
git branch -m old_branch new_branch         # Rename branch locally    
git push origin :old_branch                 # Delete the old branch    
git push --set-upstream origin new_branch   # Push the new branch, set local branch to track the new remote
```

LINKS:
- https://learngitbranching.js.org/
- https://git-scm.com/book/ru/v2
- https://services.github.com/on-demand/downloads/github-git-cheat-sheet/
