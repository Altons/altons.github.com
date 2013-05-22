---
layout: post
title: "Notes about GIT"
description: ""
category: GIT
tags: [programming, version control, unix, git]
---
{% include JB/setup %}



Notes I have gathered from different source and use as quick reminders.

##What's GIT

Git is a distributed revision control and source code management system with an emphasis on speed. Git was initially designed and developed by Linus Torvalds for Linux kernel development. 

Every Git working directory is a full-fledged repository with complete history and full revision tracking capabilities, not dependent on network access or a central server. Git is free software distributed under the terms of the GNU General Public License version 2.

> Some of these notes come from Git Book http://git-scm.com/book

##Tutorial


There is a very good interactive tutorial in Github - [here](http://try.github.com/levels/1/challenges/1)

##Installing Git



You can get it a number of ways; the two major ones are to install it from source or to install an existing package for your platform.

###Installing from Source


If you can, it's generally useful to install Git from source, because you'll get the most recent version. Each version of Git tends to include useful UI enhancements, so getting the latest version is often the best route if you feel comfortable compiling software from source.


 It is also the case that many Linux distributions contain very old packages; so unless you're on a very up-to-date distro or are using backports, installing from source may be the best bet.

To install Git, you need to have the following libraries that Git depends on: **curl, zlib, openssl, expat,** and **libiconv**. For example, if you're on a system that has yum (such as Fedora) or apt-get (such as a Debian based system), you can use one of these commands to install all of the dependencies:

```bash
  $ yum install curl-devel expat-devel gettext-devel \
    openssl-devel zlib-devel

  $ apt-get install libcurl4-gnutls-dev libexpat1-dev gettext \libz-dev libssl-dev
```

When you have all the necessary dependencies, you can go ahead and grab the latest snapshot from the Git web site:

http://git-scm.com/download

Then, compile and install:

```bash
  $ tar -zxf git-1.7.2.2.tar.gz
  $ cd git-1.7.2.2
  $ make prefix=/usr/local all
  $ sudo make prefix=/usr/local install
```
After this is done, you can also get Git via Git itself for updates:

```bash
  $ git clone git://git.kernel.org/pub/scm/git/git.git
```

###Installing on Linux


If you want to install Git on Linux via a binary installer, you can generally do so through the basic package-management tool that comes with your distribution. If youâ€™re on Fedora, you can use yum:

```bash
  $ yum install git-core
```

Or if you're on a Debian-based distribution like Ubuntu, try apt-get:

```bash
  $ apt-get install git-core
```

###Installing on Mac


There are two easy ways to install Git on a Mac. The easiest is to use the graphical Git installer, which you can download from the Google Code page:

http://code.google.com/p/git-osx-installer

The other major way is to install Git via MacPorts (http://www.macports.org). If you have MacPorts installed, install Git via

```bash
  $ sudo port install git-core +svn +doc +bash_completion +gitweb
```

You don't have to add all the extras, but you'll probably want to include **+svn** in case you ever have to use Git with Subversion repositories.

###Installing on Windows


Installing Git on Windows is very easy. The msysGit project has one of the easier installation procedures. Simply download the installer exe file from the Google Code page, and run it:

http://code.google.com/p/msysgit

After it's installed, you have both a command-line version (including an SSH client that will come in handy later) and the standard GUI.

##Setting up Git for First-Time


Now that you have Git on your system, you'll want to do a few things to customise your Git environment. You should have to do these things only once; they'll stick around between upgrades. You can also change them at any time by running through the commands again.

Git comes with a tool called git config that lets you get and set configuration variables that control all aspects of how Git looks and operates. These variables can be stored in three different places:

- ``/etc/gitconfig file``: Contains values for every user on the system and all their repositories. If you pass the option ``--system`` to git config, it reads and writes from this file specifically.
- ``~/.gitconfig file``: Specific to your user. You can make Git read and write to this file specifically by passing the ``--global`` option.
- config file in the git directory (that is, ``.git/config``) of whatever repository you're currently using: Specific to that single repository. Each level overrides values in the previous level, so values in ``.git/config`` trump those in ``/etc/gitconfig``.
- On Windows systems, Git looks for the ``.gitconfig`` file in the ``$HOME`` directory (C:\Documents and Settings\$USER for most people). It also still looks for ``/etc/gitconfig``, although it's relative to the MSys root, which is wherever you decide to install Git on your Windows system when you run the installer.

##Your Identity


The first thing you should do when you install Git is to set your user name and e-mail address. This is important because every Git commit uses this information, and it's immutably baked into the commits you pass around:

```bash
  $ git config --global user.name "John Doe"
  $ git config --global user.email johndoe@example.com
```

Again, you need to do this only once if you pass the ``--global`` option, because then Git will always use that information for anything you do on that system. If you want to override this with a different name or e-mail address for specific projects, you can run the command without the ``--global`` option when youâ€™re in that project.

##Your Editor


Now that your identity is set up, you can configure the default text editor that will be used when Git needs you to type in a message.


 By default, Git uses your system's default editor, which is generally Vi or Vim. If you want to use a different text editor, such as Emacs, you can do the following:

```bash
 $ git config --global core.editor emacs
```

##Your Diff Tool


Another useful option you may want to configure is the default diff tool to use to resolve merge conflicts. Say you want to use vimdiff:


```bash
  $ git config --global merge.tool vimdiff
```

Git accepts **kdiff3, tkdiff, meld, xxdiff, emerge, vimdiff, gvimdiff, ecmerge,** and **opendiff** as valid merge tools.

##Checking Your Settings

If you want to check your settings, you can use the git config --list command to list all the settings Git can find at that point:

```bash

  $ git config --list
  user.name=Scott Chacon
  user.email=schacon@gmail.com
  color.status=auto
  color.branch=auto
  color.interactive=auto
  color.diff=auto
  ...
```

You may see keys more than once, because Git reads the same key from different files (/etc/gitconfig and ~/.gitconfig, for example). In this case, Git uses the last value for each unique key it sees.

You can also check what Git thinks of specific keys value is by typing git config {key}:

```bash
  $ git config user.name
  Scott Chacon
```


##Commands


###Basic Commands


- ``git init``: initialize a Git repository.
- ``git status``: shows what the current state of our project is.
- ``git add``: To tell Git to start tracking changes made to any file in the repository, we first need to add it to the staging area by using **git add**. You can also type ``git add .`` The dot represents the current directory, so everything in it, and everything beneath it gets added. The files listed here are in the Staging Area, and they are not in our repository yet. We could add or remove files from the stage before we store them in the repository. You also can use wildcards if you want to add many files of the same type, i.e. ``git add '*.txt'``.
- ``git rm``: the ``git rm`` command which will not only remove the actual files from disk, but will also stage the removal of the files for us. You can use wildcards i.e. ``git rm '*.txt'``. Removing one file is great and all, but what if you want to remove an entire folder? You can use the recursive option on git rm: ``git rm -r <folder>`` This will recursively remove all folders and files from the given directory. If you happen to delete a file without using ``'git rm'`` you'll find that you still have to ``'git rm'`` the deleted files from the working tree. You can save this step by using the ``'-a'`` option on ``'git commit'``, which auto removes deleted files with the commit. ``git commit -am "Delete stuff"``.
- ``git reset <filename>``: removes a file or files from the staging area.
- ``git reset HEAD <filename>``: HEAD refers to the last commit - unstaged changes after reset.
- ``git reset --soft HEAD^``: Undoing a commit (e.g. forgot something in that commit). Move commit before HEAD.
- ``git reset --hard HEAD^``: undo last commit and all changes
- ``git checkout -- <filename>``: **Blow away all changes since last commit**. Files can be changed back to how they were at the last commit by using the command. So you may be wondering, why do I have to use this ``'--'`` thing? git checkout seems to work fine without it. It's simply promising the command line that there are no more options after the ``'--'``. This way if you happen to have a branch named octocat.txt, it will still revert the file, instead of switching to the branch of the same name.
- ``git commit -m " message "``: To store our staged changes we run the commit command with a message describing what we've changed. A *"commit"* is a snapshot of our repository. This way if we ever need to look back at the changes we've made (or if someone else does), we will see a nice timeline of all changes.
- ``git commit -a -m " message "``: Skip stagging and commit. ``-a`` add changes for all tracked files. 
- ``git commit --ammend -m "message"``: add to the last commit. Whatever has been staged is added to the last commit.
- ``git log``: Think of Git's log as a journal that remembers all the changes we've committed so far, in the order we committed them. Use ``git log --summary`` to see more information for each commit. You can see where new files were added for the first time or where files were deleted. It's a good overview of what's going on in the project.

###Remote, Cloning & Branching


- ``git remote add origin git@github.com:<username>/<repo_name>.git``: To push our local repo to the GitHub server we'll need to add a remote repository. the name "origin" is a convention not part of the command. "origin" is the local name of the remote repository. For example you could also write:

  - ``git remote add myorigin git@github.com:myname/oldrep.git``
  - ``git remote add testtest git@github.com:myname/oldrep.git``

- ``git remote rm <repo_name>``: To remove a remote repository you enter i.e. ``git remote rm origin``. Again "origin" is the name of the remote repository if you want to remove the "experimental" branch on the remote. ``git remote rm experimental``.
- ``git remote -v``: shows remote repositories
- ``git push -u origin master``: The push command tells Git where to put our commits when we're ready. The name of our remote is origin and the default local branch name is master. The -u tells Git to remember the parameters, so that next time we can simply run git push and Git will know what to do.
- ``git pull origin master``: Let's pretend some time has passed. We've invited other people to our github project who have pulled your changes, made their own commits, and pushed them. We can check for changes on our GitHub repository and pull down any new changes by running this command.
- ``git stash``: Sometimes when you go to pull you may have changes you don't want to commit just yet. One option you have, other than commiting, is to stash the changes. Use the command ``'git stash'`` to stash your changes, and ``'git stash apply'`` to re-apply your changes after your pull.
- ``git diff HEAD``: Diff between last commit and current state. The HEAD is a pointer that holds your position within all your different commits. By default HEAD points to your most recent commit, so it can be used as a quick way to reference that commit without having to look up the SHA. Another great use for ``diff`` is looking at changes within files that have already been staged. Remember, staged files are files we have told git that are ready to be committed.
- ``git diff HEAD~5``: difference 5 commits ago
- ``git diff HEAD^..HEAD``: second most recent commit vs recent commit.
- ``git branch <branch_name>``: When developers are working on a feature or bug they'll often create a copy (aka. branch) of their code they can make separate commits to. Then when they're done they can merge this branch back into their main master branch. **Branches** are what naturally happens when you want to work on multiple features at the same time. You wouldn't want to end up with a master branch which has Feature A half done and Feature B half done. Rather you'd separate the code base into two "snapshots" (branches) and work on and commit to them separately. As soon as one was ready, you might merge this branch back into the master branch and push it to the remote server.
- ``git branch``: shows your local branches (Note to myself: not sure if only shows locals).
- ``git checkout <branch_name>``: switch branches. You can use: ``git checkout -b new_branch`` to checkout and create a branch at the same time. This is the same thing as doing: ``git branch new_branch`` and then ``git checkout new_branch``.
- ``git checkout -b <branch_name>``: creates and checks out branch.
- ``git merge <branch_name>``: once you are back in your master branch, this command tells Git to merge the branch into it.
- ``git branch -d <branch_name>``: delete branch
- ``git clone https://github.com/altons/myrepo.git <folder-name>``: copy a remote repo to your local disk. Use the ``<folder-name>`` option if you want to change the name of the folder.

###Creating Remote Branches


- Why create a remote branch?
  - When you need other people to work on your branch
  - Any branch that will last more than a day

- Creating a Remote branch:
  - ``git checkout -b branch-name`` and then
  - ``git push origin branch-name`` links the local branch to the remote branch.

- Pushing to the Branch:
  - ``git add file.ext``
  - ``git commit -a -m "custom message"
  - ``git push`` pushes changes from local branch

- ``git pull`` pulling a new branches
- ``git branch -r`` list all remote branches
- ``git remote show origin`` shows info about branches
- ``git push origin :branch-name`` Deletes remote branch
- ``git branch -d branch-name`` deletes local branch manually
- ``git remote prune origin`` cleans up deleted remote branches

###Tagging

- A tag is a reference to a commit (used mostly for release versioning)
- ``git tag`` list all tags
- ``git checkout v0.0.1`` checks out code at commit
- ``git tag -a vx.x.x -m "message"`` adds a new tag
- ``git push --tags`` pushes tags to remote repo
- ``git log --oneline --decorate`` shows tags in log

###Rebase


- An alternative to merge
- Normally use under collaboration

1. ``git fetch`` syncs repo but does not merge
2. ``git rebase`` Move all changes to master which are not in origin/master to a temporary area.
3. then runs all origin/master commits.
4. and finally runs all commits in the temporary area, one at a time.


- Rebase Local branches:
  - ``git checkout local-branch-name``
  - ``git rebase master``
  - ``git checkout master``
  - ``git merge admin``

- Rebase Conflicts:
  - Fix conflicts
  - When you have resolved this problem run ``git rebase --continue``.
  - If you would prefer to skip this patch, instead run ``git rebase --skip``.
  - To check out the original branch and stop rebasing run ``git rebase --abort``.


###Checking History & Configuration


- ``git log --pretty=oneline`` displays SHA hash with commit message in one line.
- ``git log --pretty=format:"%h %ad- %s [%an]"`` you can format the log - check ``git help log`` for more options.
- ``git log --oneline -p`` is PATCH and shows differences in each file
- ``git log --online -stat``
- ``git log --online -graph`` visual representation of the branch merging into master
- ``git log --until=1.minute.ago`` until.
- ``git log --since=1.day.ago`` since(days).
- ``git log --since=1.hour.ago`` since(hours).
- ``git log --since=1.month.ago --until=2.weeks.ago`` since & until (relative).
- ``git log --since=2000-01-01 --until=2012-12-21`` since & until (absolute).

##Further Readings


- `Git Inmersion <http://gitimmersion.com/lab_03.html>`_
- `Git Ready <http://gitready.com/advanced/2009/03/11/easily-manage-git-remote-branches.html>`_
- `Git flow post <http://nvie.com/posts/a-successful-git-branching-model/>`_
- `GitHub Flow <http://scottchacon.com/2011/08/31/github-flow.html>`_  


>If you're hosting your repo on GitHub, you can do something called a pull request. A pull request allows the boss of the project to look through your changes and make comments before deciding to merge in the change. It's a really great feature that is used all the time for remote workers and open-source projects.