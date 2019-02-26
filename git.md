/* TUTORIAL:https://learngitbranching.js.org/*/
/* https://git-scm.com/book/ru/v2 */
/* CHEAT SHEET: https://services.github.com/on-demand/downloads/github-git-cheat-sheet/ */
/* check current branch and modifications */
git status

/* add file/all fiels in directory to stage */
git add <file>
git add -A .
git add '*.txt'

/* remove a file or files from the staging area. Not revert in working copy */
git reset HEAD <file>
git reset --hard HEAD
/* Files can be changed back in working copy by using:
'--'  promising the command line that there are no more options after the '--' */
git checkout -- <file>

/* REVERT FILE IN CURRENT BRANCH USING MASTER VERSION */
git checkout master src/main/java/org/optimus/model/view/View.java


/* command which will not only remove the actual files from disk, but will also stage the removal of the files for us */
git rm '*.txt'
git rm -r folder_of_cats

/* commit with comment */
git commit -m "comment"
git commit --amend //rewrite last commit 
git commit -a //add all unstaged files

/* history */
git log --summary

/* adding remote repo and naming it origin */
git remote add origin https://github.com/try-git/try_git.git

/* pushing <branch_name> to <origin_name> with -u to remember parameters */
git push -u origin master
git pull origin master
git push  <REMOTENAME> <LOCALBRANCHNAME>:<REMOTEBRANCHNAME> 
/* pushes current branch to it's sybling on the remote */
git push <REMOTENAME> HEAD
/* --rebase option tells Git to move all of commits to the tip of the master branch after synchronising it with the changes from the central repository */
git pull --rebase origin master  

/* after pull diff our most recent commit, which we can refer to using the HEAD pointer */
git diff HEAD
/* run git diff with the --staged option to see the changes you just staged */
git diff --staged

/* creating branches */
git branch new_branch
git checkout new_branch
git checkout -b new_branch

/* deletes new_branch */
$ git push --delete <remote_name> <branch_name>
$ git branch -d <branch_name>

/* merging branch1 into current branch */
git merge <branch1>
/* rebasing current branch onto top of the <branch1>. Makes consequent history. Changes in current branch and stays in it */
git rebase <branch1>

git stash:
Sometimes when you go to pull you may have changes you don't want to commit just yet. One option you have, other than commiting, is to stash the changes.
Use the command 'git stash' to stash your changes, and 'git stash apply' to re-apply your changes after your pull.
>git stash save "name1"
>git stash list
>git stash apply <number_of_stash>

/* check the list of tracked files */
git ls-tree -r master --name-only

/* how to know parent commit for commit */
 git log --pretty=%P -n 1 <commit>
 
/* list remote branches */
git branch -r | grep "BE-182"

/* rename git branch locally and remotely */
git branch -m old_branch new_branch         # Rename branch locally    
git push origin :old_branch                 # Delete the old branch    
git push --set-upstream origin new_branch   # Push the new branch, set local branch to track the new remote