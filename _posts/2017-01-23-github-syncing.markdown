---
layout: post
title:  Github Syncing
date:   2017-01-23 16:16:00 -0600
---

Commonly used git commands are `git fetch` and `git pull`. Here's a bit more about how these commands work. 

##### `git fetch`
After running this command, git will fetch all the commits from the remote repo to your local repo. But it will not make any changes to local working repository. Instead, it fetches all the new commits and stores those commits as a remote branch (separate from your local branch). This way you can see the new changes but you don't necessarily have to merge them into your local repo. This command is useful when you want to see what others are working on, but you want your local directory to remain untouched. 

##### `git merge` 
This command will integrate changes from one branch into the other branch. For example, `git merge origin/master` merges the origin branch (the remote master branch) into your local master branch

##### `git pull` and `git pull origin <branch_name>`
Running either of these commands executes both `git fetch` and `git merge` respectively. This command will integrate the remote changes into your working directory. The extra specification of origin <branch_name> will specifically pull down changes from the remote branch name specified. And if origin <branch_name> is left out git will use the current branch's remote version. 
