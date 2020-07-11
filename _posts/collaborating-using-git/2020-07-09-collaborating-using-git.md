---
title: "Collaboration using Git"
date: 2020-07-09 18:30:00 +07:00
tags: [git, version control, syncing, remote, fetch, pull, origin]
description: The blog describes collaboration using git version control.
image1: /collaborating-using-git/pull-origin1.svg
image2: /collaborating-using-git/pull-origin2.svg
image3: /collaborating-using-git/pull-origin3.svg
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
<figcaption>Fig 1. Merging commits from remote origin/master.</figcaption>
</figure>

* In the above diagram, we can see the new commit H. This commit is a **new merge commit** that contains the contents of remote A-B-C commits and has a combined log message.

<figure>
<img src="{{ page.image3 }}" alt="Rebasing remote commits">
<figcaption>Fig 1. Rebasing commits from remote origin/master.</figcaption>
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