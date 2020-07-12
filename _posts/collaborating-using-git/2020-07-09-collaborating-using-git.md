---
title: "Collaboration using Git"
date: 2020-07-09 18:30:00 +07:00
tags: [git, version control, syncing, remote, fetch, pull, origin]
description: The blog describes collaboration using git version control.
image1: /collaborating-using-git/pull-origin1.svg
image2: /collaborating-using-git/pull-origin2.svg
image3: /collaborating-using-git/pull-origin3.svg
image4: /collaborating-using-git/branching.svg
image5: /collaborating-using-git/merging1.png
image6: /collaborating-using-git/merging2.png
image7: /collaborating-using-git/before-fast-forward.svg
image8: /collaborating-using-git/3-way-merge.svg
twitter: https://twitter.com/ayush_garg341
---

In the last article I explained about getting started with git and operations that can be performed on your local system without **syncing** with remote repos. In this article we will go one step ahead of collaboration with other users and shared repos.

# 1. Syncing
### a. git remote
We all have heard the word remote, so let's define it  more explicity, *remote* is a place from which you fetch the things (web pages, youtube etc) with the help of the URL.
Similary when we set up a repo on hosting platform like Github, bitbucket, gitlab etc. we need to push and pull the changes to the repo and from the repo respectively. Now at this point we need to add this remote repo URL to our git config, so that when we push the changes git knows where it has to push.

**`Note`**:- We push and pull changes many times ( otherwise no need to use verion control ) when working on a project. So instead of typing that remote repo URL ( inconvenient ) for operations like push, pull, fetch and all other interactions with remote repo, we create an alias to that and use that alias as a reference to the URL.

The `git remote` command is essentially an interface for managing a list of remote entries that are stored in the repository's `./.git/config` file.
```
git remote
List the remote connections you have to other repositories.

git remote -v
Same as the above command, but include the URL of each connection.

git remote add <name> <remote-repo-url>
Create a new connection to a remote repository. After adding a remote, you’ll be able to use <name> as a convenient shortcut for <url> in other Git commands.

git remote rm <name
Remove the connection to the remote repository called <name>

git remote rename <old-name> <new-name>
Rename a remote connection from <old-name> to <new-name>.
```
**`Note`**:- Information is not automatically passed back and forth between repositories. Instead, developers need to manually pull upstream commits into their local repository or manually push their local commits back up to the central repository.

**The origin remote**

When you clone a repository with git clone, it automatically creates a remote connection called **origin** pointing back to the cloned repository. This behavior is also why most Git-based projects call their central repository origin.

**Repository URLs**

Git supports many ways to reference a remote repository.
###### 1. HTTP

HTTP is an easy way to allow anonymous, read-only access to a repository. But, it’s generally not possible to push commits to an HTTP address.

###### 2. SSH

For read-write access, you should use SSH instead.

```
http://host/path/to/repo.git ( http format )
ssh://user@host/path/to/repo.git ( ssh format )

ADD <NAME> <URL>
git remote add <name> <url>
Adds a record to ./.git/config for remote named <name> at the repository url <url>.
1.Accepts a -f option, that will git fetch <name> immediately after the remote record is created.
2. Accepts a --tags option, that will git fetch <name> immediately and import every tag.


GET-URL <NAME>
git remote get-url <name>
1. Accepts --push, push URLs are queried ( ask for password ) rather than fetch URLs.
2. With --all, all URLs for the remote will be listed.

SHOW <NAME>
git remote show <name>
1. Outputs high-level information about the remote <NAME>.

PRUNE <NAME>
git remote prune <name>
1. Deletes any local branches for <NAME> that are not present on the remote repository.
```

### b. git fetch
The git fetch command downloads commits, files, and refs from a remote repository into your local repo. Generally you perform fetch when you want to see what everybody else has been working on.
It is a safer way to review commits before integrating them with local repo.

**How git fetch works**
* Behind the scenes, in the repository's `./.git/objects` directory, Git stores all commits, local and remote.
* Git keeps remote and local branch commits distinctly separate through the use of branch refs.
* The refs for local branches are stored in the `./.git/refs/heads/`.
* Remote branch refs live in the `./.git/refs/remotes/` directory or we can access them using `git branch -r` 

```
git branch
====output====
master
feature1
feature2

ls ./.git/refs/heads/
====output====
master
feature1
feature2
```

**`Note`**:- Checking out a remote branch puts you in detached HEAD state. You can inspect remote branches with the usual `git checkout` and `git log` commands.

Synchronizing your local repository with a remote repository is actually a **two-step** process: **fetch, then merge**. Some command and options:-

```
git fetch <remote>
Fetch all of the branches from the repository ( also commits and files )

git fetch <remote> <branch>
Only fetched the specified branch.

git fetch --all
A power move which fetches all registered remotes and their branches.

git fetch --dry-run
The --dry-run option will perform a demo run of the command.
```

**Synchronize origin with git fetch**

Synchronizing your local repository with the central repository's master branch.
```
git fetch origin
```

To see what commits have been added to the **upstream** master, you can run a `git log` using `origin/master` as a filter:
```
git log --oneline master..origin/master
```

To approve the changes and merge them into your **local master** branch with the following commands:
```
git checkout master
git log origin/master
git merge origin/master
```

The **origin/master** and **master** branches now point to the same commit, and you are synchronized with the upstream developments.

**`Use Case`**:- You can use this strategy when other people are also working on the same branch and you need to synchronize the changes. Or when someone has updated the code online ( github, bitbucket etc ) on the same branch, then you need to fetch and then merge the changes.

### c. git push
The `git push` command is used to upload local repository content to a remote repository. This is the way how you transfer commite from local to remote repo.
**Usage**

```
git push <remote> <branch>
Push the specified branch to <remote>, along with all of the necessary commits and internal objects.

git push <remote> --all
Push all of your local branches to the specified remote.

git push <remote> --tags
Tags are not automatically pushed when you push a branch or use the --all option.
```

**`Note`**:- `git push` can be considered as 'upload' command whereas, `git fetch` and `git pull` can be thought of as 'download' commands. Once changesets have been moved via a download or upload a `git merge` may be performed at the destination to integrate the changes.

**Force Pushing**

When push requests result in a **non-fast-forward** merge, central repo refuses the request i.e. if remote history has diverged from your history, you need to pull the remote branch and merge it into local one, then try pushing again.

The `--force` flag overrides this behaviour and makes the remote repository’s branch match your local one, **deleting** any upstream changes that may have occurred since you last pulled.

**`Word of caution`**:- However, you must be absolutely certain that none of your teammates have pulled those commits before using the `--force` option.

**`Use Case`**:- You should ever need to force push when you realize that the commits you just shared were not quite right and you fixed them with a `git commit --amend` or an `interactive rebase`.

**Deleting a remote branch**

Sometimes branches need to be cleaned up for book keeping or organizational purposes. The fully delete a branch, it must be deleted locally and also remotely.
```
git branch -D branch_name
git push origin :branch_name
```

The above will delete the remote branch named `branch_name` passing a branch name prefixed with a colon to `git push` will delete the remote branch.

### d. git pull
The `git pull` command is used to fetch and download content from a remote repository and **immediately update** the local repository to match that content (`git fetch` does not update). It is actually a combination of two other commands	`git fetch` followed by `git merge`.

**How it works**
<figure>
<img src="{{ page.image1 }}" alt="pull from origin">
<figcaption>Fig 1. Pulling changes from origin/master.</figcaption>
</figure>

* In this scenario, git pull will download all the changes from the point where the local and master diverged ( here it is E ).
* `git pull` will fetch the diverged remote commits which are A-B-C.
* The pull process will then create a new local merge commit containing the content of the new diverged remote commits.

<figure>
<img src="{{ page.image2 }}" alt="Merging remote commits">
<figcaption>Fig 2. Merging commits from remote origin/master.</figcaption>
</figure>

* In the above diagram, we can see the new commit H. This commit is a **new merge commit** that contains the contents of remote A-B-C commits and has a combined log message.

<figure>
<img src="{{ page.image3 }}" alt="Rebasing remote commits">
<figcaption>Fig 3. Rebasing commits from remote origin/master.</figcaption>
</figure>

**`Note`**:- Instead of merging when pulling, we can rebase also. The `--rebase` option can be used to ensure a linear history by preventing unnecessary merge commits.


* We can now see that a rebase pull does not create the new H commit.
* Instead, the **rebase** has copied the remote commits A--B--C and appended them to the local origin/master commit history.
* In fact, pulling with `--rebase` is such a common workflow that there is a dedicated configuration option for it:

```
git config --global branch.autosetuprebase always
```

After running that command, all `git pull` commands will integrate via `git rebase` instead of git merge.


**Options**
```
git pull <remote>
Fetch the specified remote’s copy of the current branch and immediately merge it into the local copy. (eq. to git fetch <remote> and then git merge origin/<current-branch>)

git pull --no-commit <remote>
Similar to the default invocation, fetches the remote content but does not create a new merge commit.

git pull --rebase <remote>
Use git rebase instead of git merge. Simply moves your local changes onto the top of what everybody else has already contributed.

git pull --verbose
Displays the content being downloaded and the merge details.
```

**`Note`**:- The `git fetch` command can be confused with `git pull`. They are both used to download remote content. `git fetch` can be considered the "safe" option whereas, `git pull` can be considered unsafe ( because of immediate merge ).


# 2. Using branches

### a. git branch
It's importannt to understand that **branch** represents the tip of a series of commits ( effectively a **pointer** to a snapshot of your changes ), its not a container for commits. A branch represents an independent line of development as shown in below fig.
<figure>
<img src="{{ page.image4 }}" alt="Branching">
<figcaption>Fig 4. Branching.</figcaption>
</figure>

By developing them ( little feature and big feature ) in branches, it’s not only possible to work on both of them in parallel, but it also keeps the main master branch free from questionable code. Branches serve as an abstraction for the **edit/stage/commit** process. Think of them as a way to *request a brand new working directory, staging area, and project history*.

**Options**
```
git branch/git branch --list
List all of the branches in your repository.

git branch <branch>
Create a new branch called <branch>.

git branch -d <branch>
Delete the specified branch ( prevents you from deleting the branch if it has unmerged changes )

git branch -D <branch>
Force delete the specified branch, even if it has unmerged changes.

git branch -m <branch>
Rename the current branch to <branch>.

git branch -a
List all remote branches. 
```

**Creating remote branches**

The git branch command also works on remote branches. When we push a local branch to remote repo, it automatically creates that local branch at remote.
```
$ git remote add new-remote https://bitbucket.com/user/repo.git
# Add remote repo to local repo config
$ git push <new-remote> some_local_branch
# pushes the some_local_branch branch to new-remote
```

**Deleting branches**

Once you’ve finished working on a branch and have merged it into the main code base, you’re free to delete the branch without losing any history:

```
git branch -d some_local_branch
```

However, if the branch hasn’t been merged, the above command will output an error message:
```
error: The branch 'some_local_branch' is not fully merged.
If you are sure you want to delete it, run 'git branch -D some_local_branch'.
```

This protects you from losing access to that entire line of development. These commands will delete a **local copy** of a branch. The branch may still exist in remote repos. To delete a **remote branch** execute the following.
```
git push origin --delete some_local_branch
or
git push origin :some_local_branch
```

### b. git checkout
Checkout is the act of switching between different versions of a target entity. The `git checkout` command operates upon three distinct entities: **files**, **commits**, and **branches**.

By default `git checkout -b` will base the new-branch off the current HEAD.

```
$> git branch
master
new_feature_branch
another_branch

$> git checkout new_feature_branch
Switch to the existing branch new_feature_branch

$> git checkout -b <new-branch>
The above example simultaneously creates and checks out <new-branch>. The -b option is a convenience flag that tells Git to run git branch <new-branch> before running git checkout <new-branch>.

$> git checkout -b <new-branch> <existing-branch>
<existing-branch> is passed which then bases new-branch off of existing-branch instead of the current HEAD.
```

**`Note`**:- Git tracks a history of checkout operations in the reflog. You can execute `git reflog` to view the history.

**Checkout a remote branch**

In order to checkout a remote branch you have to first fetch the contents of the branch.
```
git fetch --all
git checkout <remotebranch>

git checkout -b <branchname>
git reset --hard origin/<branchname>
Checkout a new local branch and reset it to the remote branches last commit.
```

**`Note`**:- When checkout a commit you are in **DETACHED HEAD** state. Your development should always take place on a branch, never on a detached HEAD. This makes sure you always have a reference to your new commits.

### c. git merge
This is one of the important git commands. **Merging** is Git's way of putting a forked history back together again. The git merge command lets you take the independent lines of development created by git branch and integrate them into a single branch.

**How it works**

Git merge will combine multiple sequences of commits into one unified history. `git merge` is used to combine two branches. Generally `git merge` is used to combine two branches explained in the following steps:

* `git merge` takes two commit pointers, usually the branch tips, and will find a common base commit between them. Git has several different methods to find a base commit, these methods are called **"merge strategies"**

<figure>
<img src="{{ page.image5 }}" alt="Merging branches">
<figcaption>Fig 5. Common base b/w two branches.</figcaption>
</figure>

* Once Git finds a common base commit it will create a new "merge commit" that combines the changes of each queued merge commit sequence.
<figure>
<img src="{{ page.image6 }}" alt="Merging branches">
<figcaption>Fig 6. Merging feature branch into master.</figcaption>
</figure>

* Invoking this (`git merge feature_branch`)  command will merge the specified feature_branch into the current branch, we'll assume master.

* Merge commits are unique against other commits in the fact that they have **two parent commits**.

* When creating a merge commit Git will attempt to auto matically merge the separate histories for you. If Git encounters a piece of data that is changed in both histories it will be unable to automatically combine them.

**Steps to take before merge**
* Confirm the receiving branch. If needed, execute `git checkout <receiving>` to switch to the receiving branch. 

* Fetch latest remote commits. Make sure the receiving branch and the merging branch are up-to-date with the latest remote changes.

**Types of merge**
* Fast forward merge

A fast-forward merge can occur when there is a linear path from the current branch tip to the target branch.
<figure>
<img src="{{ page.image7 }}" alt="Before and after fast forward">
<figcaption>Fig 7. Before and after fast forward merge.</figcaption>
</figure>

* 3-way merge

However, a fast-forward merge is not possible if the branches have diverged. When there is **not a linear path** to the target branch, Git has no choice but to combine them via a 3-way merge. The nomenclature comes from the fact that Git uses three commits to generate the merge commit: **the two branch tips and their common ancestor**.
<figure>
<img src="{{ page.image8 }}" alt="3 way merge">
<figcaption>Fig 8. 3 way merge.</figcaption>
</figure>

**`Note`**:- After merging you can delete that branch safely using `git branch -d <branch_name>`.

**Merge conflicts**

If the two branches you're trying to merge, both changed the same part of the same file, Git won't be able to figure out which version to use. In that case you need to intervene manually, and select the changes that you want to keep.

**`Note`**:- Merge conflicts will only occur in the event of a 3-way merge. It’s not possible to have conflicting changes in a fast-forward merge.


Sample merge conflict look like below.
```
$ cat merge.txt
<<<<<<< HEAD
this is some content to mess with
content to append
=======
totally different content to merge later
>>>>>>> new_branch_to_merge_later
```

Think of these new lines as **"conflict dividers"**. The ======= line is the "center" of the conflict. All the content between the center and the <<<<<<< HEAD line is content that exists in the current branch master which the HEAD ref is pointing to. Alternatively all content between the center and >>>>>>> new_branch_to_merge_later is content that is present in  merging branch. You can resolve these merge conflicts in your favorite text editor.

*This is the end of this article on git collaboration. All these operations discussed are used to sync your changes with remote repo. In the upcoming article on git we'll look at some advance git stuff, before that make sure you understand the git properly. Stay in touch. Please follow on <a href="{{page.twitter}}" target="_blank">Twitter</a>*
