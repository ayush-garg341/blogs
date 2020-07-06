---
title: "Git: From Scratch"
date: 2020-07-04 21:30:00 +07:00
tags: [git, version control]
description: The blog describes how to use git version control.
image1: /git-from-scratch/staging.svg
image2: /git-from-scratch/stash.svg
image3: /git-from-scratch/log.svg
image4: /git-from-scratch/revert.svg
image5: /git-from-scratch/before-reset.png
image6: /git-from-scratch/after-reset.png
image7: /git-from-scratch/reset-affect.svg
image8: /git-from-scratch/rebase.svg
twitter: https://twitter.com/ayush_garg341
---

This post is for all those who wants to learn to use GIT in development. I am assuming you know what is git and why we use it. If you want to learn and understand GIT, learn it by doing. At every step create a repo and run the commands and observe the output. So without further ado, let's jump to setting up a repo and using git.

# 1. Setting up a repo
## a. git init
The **git init** command creates a new Git repository. Executing git init creates a .git subdirectory in the current working directory, which contains all of the necessary Git metadata for the new repository. By default, git init will initialize the Git configuration to the **.git** subdirectory path.

```
mkdir git-demo
cd git-demo
git init
```

Upon inspecting the folder **git-demo** (use *ls -ah*, since .git folder is hidden), you will find .git folder which contains all the git configuration for the project.

### git init options
#### 1. - - bare
Creates a repository, but it does not have the working directory. This means that you can not edit files, commit your changes, add new files in that repository.<br>
Bare repositories are usually central repositories where everyone moves their work to. There is no need to manipulate the actual work. It's a way to synchronize efforts between multiple people.<br>
Think of - - bare as a way to mark a repository as a storage facility, as opposed to a development environment. The most common use case for  git init - - bare is to create a remote central repository.<br>
Bare version of a repository called git-demo should be stored in a directory called git-demo.git.
The only possible operations on the Bare Repository are **Pushing** or **Cloning**.

```
git init --bare <directory>
```

#### 2. - - separate-git-dir
With the help of this option we can store **.git** folder at some other location.
```
git init --separate-git-dir=<git dir>
git init --separate-git-dir /home/<user_name>/Test.git Test
```

The working directory “Test” only contains a file called .git (instead of a directory called .git). This file contains a **pointer** to where the actual repository is.
Actual repo will be located inside **Test.git** folder.
You can call git init - - separate-git-dir on an existing repository and the .git dir will be moved to the specified *git dir* path.

Some common use case for - - separate-git-dir are:-

*  To keep your system configuration "dotfiles" (.bashrc, .vimrc, etc.) in the home directory while keeping the .git folder elsewhere
*  Your Git history has grown very large in disk size and you need to move it elsewhere to a separate high-capacity drive
*  You want to have a Git project in a publicly accessible directory like `www:root`


#### 3. - - template
With this option we initialize our repo with the existing template.
```
git init <directory> --template=<template_directory>
mkdir -p /path/to/template \
echo "Hello World" >> /absolute/path/to/template/README \
git init /new/repo/path --template=/absolute/path/to/template \
cd /new/repo/path \
cat /new/repo/path/README
```


## b. git clone
Git clone is primarily used to point to an existing repo and make a clone or copy of that repo in a new directory, at another location. <br>
Cloning automatically creates a remote connection called **origin** pointing back to the original repository.<br>
The example below demonstrates how to obtain a local copy of a central repository stored on a server accessible at *example.com* using the *SSH* username *test*. <br>
```
git clone ssh://test@example.com/path/to/my-project.git
cd my-project
# Start working on the project
```

**`Note`** that the .git extension is omitted from the cloned repository. This reflects the non-bare status of the local copy.

```
cloning to specific folder
git clone <repo> <directory>

cloning a specific tag
git clone --branch <tag> <repo>

Shallow clone.
git clone -depth=1 <repo>
```

In case of **shallow clone** the repository located at *repo*  only clone the history of commits specified by the option depth=1. In this example a shallow clone of *repo* is made and only the **most recent commit** is included in the new cloned Repo.

An extensive commit history may cause **scaling problems** such as disk space usage limits and long wait times when cloning.

```
git clone -branch
```

The **-branch** argument lets you specify a specific branch to clone instead of the branch the remote HEAD is pointing to, usually the master branch.

Git clone URLs type:
* SSH
* HTTPS
* GIT

The problem with **https** is that you need to enter credentials everytime you do any git operation involving remote ( fetch, pull, push ). While for **ssh** your system key needs to be present in repo hosting platform (github, bitbucket).


## c. git config
The git configuration file is present at 3 places in your system and each has different priority order.

* **local** by default, git config will write to a local level if no configuration option is passed. Local level configuration is applied to the context repository git config gets invoked in ( present at .git/config ).

* **global** global level configuration is user-specific, meaning it is applied to an operating system user ( present at ~/.gitconfig on unix systems ).

* **system** system-level configuration is applied across an entire machine. This covers all users on an operating system and all repos.

**`Priority order`** is local > global > system

```
We can also customize git command output colors using git config.
You can create a section [color] in your ~/.gitconfig with e.g. the following content:
[color]
  diff = auto
  status = auto
  branch = auto
  interactive = auto
  ui = true
  pager = true

You can also fine control what you want to have coloured in what way, e.g

[color "status"]
  added = green
  changed = red bold
  untracked = magenta bold

[color "branch"]
  remote = yellow

For the above changes to take effect you need a terminal which supports colour.
```

## d. git alias
Aliases are used to create shorter commands that map to longer commands. It is important to note that there is **no direct git alias** command. Aliases are created through the use of the git config command and the Git configuration files.
```
$ git config --global alias.co checkout
$ git config --global alias.br branch
$ git config --global alias.ci commit
$ git config --global alias.st status

or put the below commands in ~/.gitconfig
[alias]
        co = checkout
        br = branch
        ci = commit
        st = status
```
Using Git aliases will make you a faster and more efficient developer.

# 2. Saving changes

## a. git add
The git add command adds a change in the **working directory** to the **staging area**.
Staging Area acts as a buffer between the working directory and the project commit history.
<figure>
<img src="{{ page.image1 }}" alt="staging area">
<figcaption>Fig 1. Working dir, staging area and commit history.</figcaption>
</figure>
```
Developing a project involves a pattern
edit ( working directory ) -> stage ( git add file_name )  -> commit ( git commit <commit_msg>  )
```
### git add options
#### 1. -p
The -p option with git add lets you  choose portions of a file to add to the next commit. This gives you more fine control on a file, what needs to be added to staging area after you are done editing a file.
```
git add -p <file_name>
```

## b. git commit
Captures a snapshot of the project's currently staged changes. This is analogous to, when you start making your resume and then upgrade and rename it according to your skills, to save different versions of the resume.

This is a **local** operation, remember and has nothing to do with remote repo until we explicitly **git push** our changes. Each developer’s local repository is a buffer between their contributions and the central repository.

```
git commit
```
Commit the staged snapshot. Will open a text editor to type the commit msg.

```
git commit -a
```
Commit a snapshot of all changes in the working directory. Please **note** this only includes modifications to tracked files, not untracked files.

```
git commit -m "commit message"
```
A shortcut command that immediately creates a commit with a passed commit message.
Will not open a text editor to type the commit msg.

```
git commit --amend
```
Passing this option will modify the last commit. 
Instead of creating a new commit, staged changes will be added to the previous commit. This command will open up the system's configured text editor and prompt to change the previously specified commit message.


## c. git diff
Diffing is a function that takes two input data sets and outputs the changes between them.
Multi-use Git command that when executed runs a diff function on Git data sources ( commits, branches, files ). We use this feature when we want to see the difference in changes.

```
cd git-demo
touch diff_test.txt
echo "this is a git diff test example" > diff_test.txt
git init .
git add diff_test.txt
git commit -am "add diff test file"

echo "this is a diff example" > diff_test.txt
git diff

==========Output==============
diff --git a/diff_test.txt b/diff_test.txt 
index 6b0c6cf..b37e70a 100644
--- a/diff_test.txt
+++ b/diff_test.txt
@@ -1 +1 @@
-this is a git diff test example
+this is a diff example
```
#### Explaining output
* First line in the output is input sources of diff.
* Second line displays some internal Git metadata.

```
--- a/diff_test.txt
+++ b/diff_test.txt
```
These lines are a legend that assigns symbols to each diff input source. - - - shows some thing has been removed +++ shows something has been added.

```
@@ -1 +1 @@
-this is a git diff test example
+this is a diff example
```

The remaining diff output is a list of diff 'chunks'. A diff only displays the sections of the file that have changes. In our current example, we only have one chunk as we are working with a simple scenario. In our simplified example, we have -1 +1 meaning line one had changes.

```
In a more realistic diff, you would see a header like:
@@ -34,6 +34,8 @@
```
In this header example, 6 lines have been extracted starting from line number 34. Additionally, 8 lines have been added starting at line number 34.

#### Diffing binary files
Git diff can be run on binary files.
```
git diff
Binary files a/script.pdf and b/script.pdf differ
```
Unfortunately, the default output is not very helpful.

Git does have a feature that allows you to specify a shell command to transform the content of your **binary files into text** prior to performing the diff. It does require a little set up though.
First, you need to specify a **textconv** filter describing how to convert a certain type of binary to text ( ~/.gitconfig  ).

```
[diff "pdfconv"]
textconv=pdftohtml -stdout
```

Then all you need to do is associate one or more file patterns with our **pdfconv** filter. You can do this by creating a **.gitattributes** file in the root of your repository.
```
*.pdf diff=pdfconv
```
Once configured, git diff will first run the binary file through the configured converter script and diff the converter output.

```
compare staged file changes.
git diff ./path/to/file
```

Git diff can be passed Git refs to commits to diff. Some example refs are, **HEAD, tags and branch names**.

```
comparing files between two different commits
git diff commit_id1 commit_id2

comparing branches
git diff branch1 other-branch

comparing files from two branches
git diff master new_branch ./diff_test.txt
```

## d. git stash
Temporarily shelves (or stashes) changes you've made.

**Use case** :-
Suppose we are working on a feature that's not been completed yet and our code in production has got some issue/bug, then we need to look at that issue. At such times we are not ready to commit our feature, at that time we stash our feature changes temporarily and look at issue and fix that first. Later we re-apply our stashed changes.

###### Stashing
The git stash command takes your **uncommitted changes** (both staged and unstaged), saves them away for later use, and then reverts them from your working copy.
At this point you're free to make changes, create new commits, switch branches, and perform any other Git operations.
**`Note`** that the stash is local to your Git repository; stashes are not transferred to the server when you push.
```
$ git status
On branch master
Changes to be committed:
new file: style.css
Changes not staged for commit:
modified: index.html
$ git stash
Saved working directory and index state WIP on master: 5002d47 our new homepage
HEAD is now at 5002d47 our new homepage
$ git status
On branch master
nothing to commit, working tree clean
```
**WIP**:- Work in progress
###### Re-applying stashed changes
You can reapply previously stashed changes with `git stash pop`. *Popping* your stash removes the changes from your stash and reapplies them to your working copy.

Alternatively, you can reapply the changes to your working copy and keep them in your stash with `git stash apply`.( useful if you want to apply the same stashed changes to multiple branches. )

```
$ git status
On branch master
nothing to commit, working tree clean
$ git stash pop
On branch master
Changes to be committed:
new file: style.css
Changes not staged for commit:
modified: index.html
Dropped refs/stash@{0} (32b3aa1d185dfe6d57b3c3cc3b32cbf3e380cc6a)


$ git stash apply
On branch master
Changes to be committed:
new file: style.css
Changes not staged for commit:
modified: index.html
```

**`Caveat`**:- Git won't stash changes made to untracked or ignored files.

###### Stashing un-tracked or ignored files.
By default, running git stash will stash:
* changes that have been added to your **index** (staged changes).
* changes made to files that are currently tracked by Git (unstaged changes)

But it will **not** stash:
* new files in your working copy that have not yet been staged
* files that have been ignored

Adding the **-u option** (or --include-untracked) tells git stash to also stash your untracked files.
You can include changes to ignored files as well by passing the **-a option** (or --all) when running git stash.

<figure>
<img src="{{ page.image2 }}" alt="stash">
<figcaption>Fig 2. Stashing tracked, un-tracked and ignored files..</figcaption>
</figure>

## e. .gitignore
Ignored files are tracked in a special file named **.gitignore** that is checked in at the root of your repository.

There is no explicit git ignore command: instead the .gitignore file must be edited and committed by hand when you have new files that you wish to ignore. .gitignore files contain patterns that are matched against file names in your repository to determine whether or not they should be ignored.

**Use Case**: Suppose you do not want to track certain files or directories, at such times you include them in this special file. Example for this is `node_modules` folder when developing node/react project or it can be `local_settings.py` when developing django project.

There are certain  <a href="https://www.atlassian.com/git/tutorials/saving-changes/gitignore" target="_blank">rules and patterns</a>, that you can include in this file to ignore files you do not want to be tracked.

###### Ignoring previously committed file
For this you need to delete the file from your repository and then add a .gitignore rule for it.
Using the **- - cached** option with git rm means that the file will be deleted from your repository, but will remain in your working directory as an ignored file.
```
$ echo debug.log >> .gitignore
$ git rm --cached debug.log
rm 'debug.log'
$ git commit -m "Start ignoring debug.log"
```

Omit the **- - cached** option if you want to delete the file from both the repository and your local file system.


###### Committing an ignored file
It is possible to force an ignored file to be committed to the repository using the **-f (or - - force)** option with git add.
```
$ cat .gitignore
*.log
$ git add -f debug.log
$ git commit -m "Force adding debug.log"

========more obvious approach==========
$ echo !debug.log >> .gitignore
$ cat .gitignore
*.log
!debug.log
$ git add debug.log
$ git commit -m "Adding debug.log"
```

###### Debugging .gitignore files
**Use Case**: Sometimes we add so many pattern in our .gitignore file and accidentally add those files also which needs to be tracked. At that time we need to dubug which pattern is causing that file to be ignored.

You can use the **git check-ignore** command with the **-v (or - - verbose)** option to determine which pattern is causing a particular file to be ignored:
```
$ git check-ignore -v debug.log
.gitignore:3:*.log debug.log

=========output=========
<file containing the pattern> : <line number of the pattern> : <pattern> <file name>
```

# 3. Inspecting a repo
**Use Case**: Sometime when we clone the existing repo or develop our own project, we may need to see the existing branches, commits. logs etc.

#### a. git status
Displays the state of the working directory and the staging area. It lets you see which changes have been staged, which haven’t, and which files aren’t being tracked by Git.
It simply shows you what's been going on with `git add` and `git commit`. Sample output of the command `git status` is as follows:
```
On branch master
Changes to be committed:
(use "git reset HEAD <file>..." to unstage)

modified: hello.py

Changes not staged for commit:
(use "git add <file>..." to update what will be committed)
(use "git checkout -- <file>..." to discard changes in working directory)

modified: main.py

Untracked files:
(use "git add <file>..." to include in what will be committed)

hello.pyc
```

#### b. git log
The  `git log` command displays committed snapshots. It lets you list the project history, filter it, and search for specific changes. It only operates on the committed history.
<figure>
<img src="{{ page.image3 }}" alt="git status vs. git log">
<figcaption>Fig 3. Difference between git status and git log.</figcaption>
</figure>

```
Display the entire commit history using the default formatting.
git log

Limit the number of commits by <limit>.
git log -n <limit>

Condense each commit to a single line.
git log --oneline

Along with the ordinary git log information, include which files were altered and the relative number of lines that were added or deleted from each of them.
git log --stat

Display the patch representing each commit. Most detailed view you can have of your project history.
git log -p

Search for commits by a particular author. 
git log --author="<pattern>"

Search for commits with a commit message that matches <pattern>.
git log --grep="<pattern>"

Show only commits that occur between <since> and <until>. Both arguments can be either a commit ID, a branch name, HEAD.
git log <since>..<until>

Only display commits that include the specified file
git log <file>

By default, git log will only show commits for the currently selected branch. It is entirely possible that the commit you're looking for is on another branch. You can view all commits across all branches
git log --branches=*

For specific branch
git log <branch_name>
```

`git log` is very powerful tool to inspect the project history. It comes with several options that can be combined together to fine control the output of the command.


#### c. git tag
Tags are ref's that point to specific points in Git history. Tagging is generally used to capture a point in history that is used for a marked version release (i.e. v1.0.1). A tag is like a branch that does not change.
```
Create a tag.
git tag <tagname>

eg. git tag v1.4

```

Git supports two types of tags.
* Annotated tags (public)
* Lightweight tags  (private)

They differ in the amount of accompanying meta data they store. Annotated tags store extra meta data such as: the tagger name, email, and date.

```
Creating annotated tags
git tag -a v1.4 -m "my version 1.4"
git tag -a v1.4 older-commit-id

Lighweight tags are created  with abscence of -a, -s or -m options
git tag v1.4-lw

Listing tags
git tag

Listing tags with wildcard
git tag -l *-rc*

```
**rc** stands for *release candidates*

**`Note`**:- By default, git tag will create a tag on the commit that HEAD is referencing. 

###### Pushing tags to remote
By default tags are not pushed to origin. They need to be pushed explicitly.
```
$ git push origin v1.4
Counting objects: 14, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (12/12), done.
Writing objects: 100% (14/14), 2.05 KiB | 0 bytes/s, done.
Total 14 (delta 3), reused 0 (delta 0)
To git@bitbucket.com:atlasbro/gittagdocs.git
* [new tag] v1.4 -> v1.4
```

###### Checking out tags
You can view the state of a repo at a tag by using the git checkout command.
```
git checkout v1.4
```
**`Note`**:- This puts the repo in **DETACHED HEAD** state.

In “detached HEAD” state, if you make changes and then create a commit, the tag will stay the same, but your new commit won’t belong to any branch and will be unreachable, except by the exact commit hash.
Thus, if you need to make changes — say you’re fixing a bug on an older version, for instance — you will generally want to create a branch:
`git checkout -b version2 v1.4`

**What exactly is detached HEAD state?**

If you are working in your repo and do **git checkout \<SHA\>** you will be in a "detached HEAD". You are not on a branch. You are checked out to a specific instance in the history.
You would need to create a branch in order to commit/push changes because you would be creating commits that would be "in limbo" with no way to identify them other than the SHA. Git will remove the commit during **git garbage collection** process  because of it not being on a branch.

```
Delete the tag with -d option
git tag -d v1
```


#### d. git blame
This command is basically troubleshooting command. The high-level function of `git blame` is the display of author metadata attached to specific committed lines in a file. When several people are working on a file, there are many changes and you want to track which change has been made by whom.
This is used to explore the history of specific code and answer questions about what, how, and why the code was added to a repository.
**`Note`**:- git blame only operates on individual files.

```
git blame -L 1,5 <file_name>
The -L option will restrict the output to the requested line range. (1 to 5)

git blame -e <file_name>
The -e option shows the authors email address instead of username.

git blame -w <file_name>
The -w option ignores whitespace changes.

git blame -M <file_name>
The -M option detects moved or copied lines within in the same file. This will report the original author of the lines instead of the last author that moved or copied the lines.

git blame -C <file_name>
The -C option detects lines that were moved or copied from other files. This will also report original author.
```

###### git blame vs. git log
While `git blame` displays the last author that modified a line, often times you will want to know when a line was originally added. To list all original commits in-which a specific code piece was added or modified execute `git log` with the **-S** option.
```
git log -S'<text_line>' <file_name>
git log -S'<text_line>' --pretty=format:'%h %an %ad %s'  <file_name>
```

# 4. Undoing changes
When **undoing** in Git, you are usually moving back in time, or to another timeline where mistakes didn't happen. When you have found a commit reference to the point in history you want to visit, you can utilize the `git checkout` command to visit that commit.

**`Note`**:- During the normal course of development, the HEAD usually points to master or some other local branch, but when you check out a previous commit, HEAD no longer points to a branch, it points directly to a commit. This is called a “detached HEAD” state.

#### a. git checkout
By the command we can checkout ( go back )  previous commits or switch to other branches or restore the previous version of a file by undoing the chnages in it.

Checking out an old file does not move the HEAD pointer. It remains on the same branch and same commit, avoiding a 'detached head' state. Serves as a way to revert back to an old version of an individual file.

###### Undo a commit with git checkout
Let;s take an example of commit history shown below.
```
git log --oneline
872fa7e Try something crazy
a1e8fb5 Make some important changes to hello.txt
435b61d Create hello.txt
9773e52 Initial import
```
We will focus on undoing the 872fa7e. Steps included as follows:
1. checkout previous commit i.e. (a1e8fb5 using `git checkout a1e8fb5`)
2. Now we will be in Detached head state.
3. Create and move to new branch from this detached head state i.e. `git checkout -b new_branch`
4. The repo is now on a new history timeline in which the 872fa7e commit no longer exists.
5. Commit 872fa7e has become orphaned and will be ultimately removed by git garbage collector.

But this undo strategy is not appropriate.

#### b. git clean
This command operates on **un-tracked** files. When finally executed git clean is not undo-able. When fully executed, git clean will make a hard filesystem deletion, similar to executing the command line **rm** utility.

**git clean options**

```
git clean -n
The -n option will perform a “dry run” of git clean. This will show you which files are going to be removed without actually removing them.

git clean -f
Removing untracked_file

git clean -dn
Would remove untracked_dir/

git clean -df
Removing untracked_dir/

git clean -xf
Rmoving ignored files, not just project build specific ones.

git clean -i
To make removal interactive
```

#### c. git revert
Instead of removing the commit from the project history, it figures out how to **invert** the changes introduced by the commit and appends a new commit with the resulting inverse content.

**Use Case**: This prevents Git from **losing history**, which is important for the integrity of your revision history and for reliable collaboration. Also if you’re tracking down a bug and find that it was introduced by a single commit. Instead of manually going in, fixing it, and committing a new snapshot, you can use `git revert` to automatically do all of this.

<figure>
<img src="{{ page.image4 }}" alt="revert">
<figcaption>Fig 4. git reverting.</figcaption>
</figure>

**How it works?**
```
mkdir git-demo
cd git-demo
git init .
touch test-file.txt

git status
git add test-file.txt
git commit -am "intial commit"

echo "some text added" >> test-file.txt
git add test-file.txt
git commit -am "text added"

echo "some more text added to make it longer" >> test-file.txt
git add test-file.txt
git commit -am "more text added"

git log --oneline

========ex. output==========
86bb32e more text added
3602d88 text added
299b15f initial commit
```
**`Note`**: Git revert expects a commit ref was passed in and will not execute without one. Always remember HEAD points to the latest commit or to the tip of your branch.
```
git revert HEAD

# enter commit msg. in text editor.

git log --oneline

=======ex. output==========
1061e79 Revert "more text added" 
86bb32e more text added
3602d88 text added
299b15f initial commit
```

**`Note`**:-
* 3rd commit is still in history
* 2nd and 4th has exactly same code base
* Does not change commit history and integrity maintained.
* Able to target an individual commit at an arbitrary point in the history.
* Elegant undo solution.


#### d. git reset
This command operates on "The Three Trees of Git". These tress are Commit History (HEAD), the Staging Index, and the Working Directory. Let's understand these three trees in detail:
###### Working directory
This tree is in sync with local file system and is representative of the immediate changes made to content in files and directories.
When we add a new file or change an already existing file, these files are part of working directory tree.
```
mkdir git-demo
cd git-demo
git init .

touch first-file.txt
echo "some content" >> first-file.txt
git add first-file.txt
git commit -am "intial commit"

echo "some more content added" >> first-file.txt
git status

On branch master 
Changes not staged for commit: 
(use "git add ..." to update what will be committed) 
(use "git checkout -- ..." to discard changes in working directory) 
modified: first-file.txt
```

###### staging index
This tree is tracking Working Directory changes, that have been promoted with `git add`, to be stored in the next commit. In simple words when we run `git add` on modified files or newly added files they become part of staging index/tree.

To accurately view the state of the Staging Index we must utilize a lesser known Git command `git ls-files`. This command is essentially a debug utility for inspecting the state of Staging Index tree.

**`Note`**:- The Commit History stores its own object SHA's for identifying pointers to commits and refs and the Staging Index has its own object SHA's for tracking versions of files in the index.
```
git add first-file.txt
git status
On branch master Changes to be committed: 
(use "git reset HEAD ..." to unstage) 
modified: first-file.txt
```
After adding `first-file.txt` using `git add` it has become the part of staging index/tree.

###### commit history
The `git commit` command adds changes to a permanent snapshot that lives in the Commit History. This snapshot also includes the state of the Staging Index at the time of commit.
```
git commit -am"update content of first-file"
[master dc67808] update content of first-file
1 file changed, 1 insertion(+)
git status
On branch master
nothing to commit, working tree clean
```
Now executing git log will display the Commit History.

**How it works?**

`git reset`, moves both the HEAD and branch refs to the specified commit.
<figure>
<img src="{{ page.image5 }}" alt="before reset">
<figcaption>Fig 5. Before reset.</figcaption>
</figure>

<figure>
<img src="{{ page.image6 }}" alt="after reset">
<figcaption>Fig 6. After running git reset b</figcaption>
</figure>

**git reset options**
<figure>
<img src="{{ page.image7 }}" alt="reset scope and options">
<figcaption>Fig 7. Reset scope and options</figcaption>
</figure>

##### 1. - - hard
As shown in the above picture `git reset --hard` or `git reset --hard <commit>` the commit history ref pointers are updated to the specified commits and staging index and working directory are reset to match that of the specified commit. This means any pending work in staging index and working directory will be lost.

##### 2. - - mixed
This is the default operating mode `git reset` or `git reset <commit>`. The ref pointers are updated. The Staging Index is reset to the state of the specified commit. But the changes in **working directory** remain as it is. Any changes that have been undone from the Staging Index are moved to the Working Directory.

##### 3. - - soft
When the --soft argument is passed, the ref pointers are updated and the reset stops there. The Staging Index and the Working Directory are left untouched.

**`Note`**:- `git reset` is designed to undo local changes to the Staging Index and Working Directorry. You should never use `git reset <commit>` when any snapshots after `<commit>` have been pushed to a public/shared repository. For this purpose use `git revert` command when commit is pushed to public repo.

##### Unstaging a file
The `git reset` command is frequently encountered while preparing the staged snapshot.
```
# Edit both config.js and index.js
# Stage everything in the current directory
git add .
# Realize that the changes in config.js and index.js
# should be committed in different snapshots
# Unstage index.js
git reset index.js
# Commit only config.js
git commit -m "Make some changes to config.js"
# Commit index.js in a separate snapshot
git add index.js
git commit -m "Edit index.js"
```

`git reset` helps you keep your commits **highly-focused** by letting you **unstage** changes that aren’t related to the next commit.

#### e. git rm
The `git rm` command is used to **remove files** from a Git repository. It can be thought of as the inverse of the `git add` command. The primary function of `git rm` is to **remove tracked files** from the Git index. Additionally it can be used to remove files from both the staging index and the working directory.

**git rm options**
```
git rm <file>
Specifies the target file to remove or space delimited list of files file1 file2 file3.

git rm -n <file>
git rm --dry-run <file>
The "dry run" option is a safeguard that will execute the git rm command but not actually delete the files.

git rm -r <dir_path>
The -r option is shorthand for 'recursive' (for directory).

git rm --cached <file>
The cached option specifies that the removal should happen only on the staging index.
```

**Undo git rm operation**

Executing `git rm` is not a permanent update. The command will update the staging index and the working directory. These changes will not be persisted until a new commit is created and the changes are added to the commit history.
```
git reset HEAD

git checkout .
```
A reset will revert the current staging index and working directory back to the HEAD commit. A checkout will have the same effect and restore the latest version of a file from HEAD. The `git rm` command operates on the current branch only. The file removal is not persisted to the repository history until a new commit is created.

**`Note`**:- `git rm` is equivalent to shell **rm** command and `git add`. A Git repository will recognize when a regular shell rm command has been executed on a file it is tracking. It will update the **working directory** to reflect the removal. It will **not** update the staging index with the removal. An additional `git add` command will have to be executed on the removed file paths to add the changes to the staging index.  


# 5. Re-writing history
Using re-writing history commands comes under the disclaimer that using them may result in lost content. Sometimes we need to merge several commits into one to make branch commit history look clean, merge one branch into another these are some use cases when we need to rewrite commit history. Below are the methods explained in detail:

#### a. git commit - - amend
**Use Case**:- Suppose you have edited some files and added them in staging index, now before committing these changes you realize that these changes should go with the last commit you made. Or there is another scenario, when you forgot to add a file in staging tree and later realize that code won't work without it, in this case instead of creating new commit, you can amend the file with the last commit you made.

**`Note`**:- Amending does not just alter the most recent commit, it replaces it entirely, meaning the amended commit will be a new entity with its own ref.

```
git commit --amend
git commit --amend -m "an updated commit message"
```
Let's say you just committed and you made a mistake in your commit log message. Running this command when there is nothing staged lets you edit the previous commit’s message without altering its snapshot.

```
#edit config.js and index.js
git add config.js
git commit

#realize you forgot to add the changes from index.js
git add index.js
git commit --amend --no-edit
```

The **- - no-edit** flag will allow you to make the amendment to your commit without changing its commit message.

**`Note`**:- Amended commits are actually entirely new commits and the previous commit will no longer be on your current branch. Avoid amending a commit that other developers have based their work on i.e **don't amend public commits**. Amending a commit that is shared with another user will potentially require confusing and lengthy merge conflict resolutions.

#### b. git rebase
Rebasing is the process of moving or combining a sequence of commits to a new base commit. Rebase is one of two Git utilities (another is `git merge`)  that specializes in integrating changes from one branch onto another.
<figure>
<img src="{{ page.image8 }}" alt="rebase">
<figcaption>Fig 8. Visualize rebasing flow.</figcaption>
</figure>

From a content perspective, rebasing is **changing the base** of your branch from one commit to another making it appear as if you'd created your branch from a different commit.


**`Note`**:- The primary reason for rebasing is to maintain a **linear project history** which is not possible via merge. Rebasing is a common way to integrate upstream changes into your local repository. You should never rebase commits once they've been pushed to a public repository.

```
In the above picture we have change the base of our feature branch from older master branch commit to the newer master commit.

git branch
git checkout <feature_branch>
git rebase master
```

**`Caveats`**:- Merge conflicts may become more frequent during a rebase workflow. This occurs if you have a long-lived branch that has strayed from master. Eventually you will want to rebase against master and at that time it may contain many new commits that your branch changes may conflict with. The real danger cases arise when executing history rewriting interactive rebases and force pushing the results to a remote branch that's shared by other users.

#### c. git reflog
**Use Case**:- When you use `rebase -i` in interactive mode, you might lost some commits and later you realized that you need them. Or you have accidentally deleted a branch (`git branch -d <branch_name>` gives the SHA1), now you want to recover that. In such situations `reflog` is a saviour.

The `reflog` command keeps a track of every single change made in the references (branches or tags) of a repository and keeps a log history of the branches and tags that were either created locally or checked out. The reflog command will give you a good history of what's been happening on the head of your branches. It might look like below
```
$git reflog

930b2f3 HEAD@{9}: commit: another file added
db9839d HEAD@{10}: checkout: moving from ayush_local to master
db9839d HEAD@{11}: checkout: moving from master to ayush_local
db9839d HEAD@{12}: pull origin ayush_local: Fast-forward
d79a78c HEAD@{13}: checkout: moving from ayush_local to master
db9839d HEAD@{14}: commit: commit from another branch
d79a78c HEAD@{15}: checkout: moving from master to ayush_local
d79a78c HEAD@{16}: commit (initial): first commit
```

**`Note`**:- This is useful when you have lost some commits and wondering what to do now. Use this command to see older commits and then checkout to correct branch and run `git reset <SHA1>` to recover lost commits.
Executing `git reflog show` will display the log for the passed \<refid\>.

For more in depth detail on <a href="https://git-scm.com/docs/git-reflog" target="_blank">reflog</a>

*This is the end of this article on git basics. All these operations can be performed on your local system without interacting with remote repos. In the upcoming article we'll see how to collab with remote repos. Stay in touch. Please follow on <a href="{{page.twitter}}" target="_blank">Twitter</a>*
