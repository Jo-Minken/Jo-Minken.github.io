---
layout: post
title: "Git Tutorial Notes"
date: 2020-11-24
categories: [Git]
---

A note taking from [tutorialspoint - Git Tutorial](https://www.tutorialspoint.com/git/index.htm)

## Basic Concepts
A note taking from [Git - Basic Concepts](https://www.tutorialspoint.com/git/git_basic_concepts.htm)

Distributed Version Control System (CVCS) uses a central server to store all files and enables team collaboration.

### DVCS Terminologies
#### Local Repository
Every VCS tool provides a private workplace as a working copy. Developers make changes in their private workplace and after commit, these changes become a part of the repository.

#### Working Directory and Staging Area or Index
Working Directory ー（Git add operation）→ Staging Area ー（Git commit operation）→ Git repository

#### Blobs (named as SHA1 hash)
Blob stands for Binary Large Object. Each version of a file is represented by blob.

#### Trees (named as SHA1 hash)
Tree is an object, which represents a directory.

#### Commits (named as SHA1 hash)
Commit holds the current state of the repository.

#### Branches
Every branch is referenced by HEAD, which points to the latest commit in the branch.

#### Tags
Tag assigns a specific version in the repository. 
Tags are very similar to branches, but the difference is that tags are **immutable**.

#### HEAD
HEAD is a pointer, which always points to the latest commit in the branch. 
The heads of the branches are stored in **.git/refs/heads/** directory.
```
$ ls -1 .git/refs/heads/
master

$ cat .git/refs/heads/master
570837e7d58fa4bccd86cb575d884502188b0c49
```

#### Clone
Clone operation mirrors the complete repository.

#### Pull
Pull operation copies the changes from a remote repository instance to a local one.

#### Push
Push operation copies changes from a local repository instance to a remote one.

#### Revision
Revision represents the version of the source code. Revisions in Git are represented by commits. These commits are identified by SHA1 secure hashes.

## Environment Setup
A note taking from [Environment Setup](https://www.tutorialspoint.com/git/git_environment.htm)

Listing Git settings:
```
$ git config --list
```

### Setting user info
```
# setting username
$ git config --global user.name "Jerry Mouse"

# setting email id
$ git config --global user.email "jerry@tutorialspoint.com"
```

### Avoid merge commits for pulling
```
$ git config --global branch.autosetuprebase always
```

## Git Basic Commands
A note taking from:
- [Git - Review Changes](https://www.tutorialspoint.com/git/git_review_changes.htm)
- [Git - Commit Changes](https://www.tutorialspoint.com/git/git_commit_changes.htm)

```
# git log: view log details
$ git log
commit cbe1249b140dad24b2c35b15cc7e26a6f02d2277
Author: Jerry Mouse <jerry@tutorialspoint.com>
Date: Wed Sep 11 08:05:26 2013 +0530

Implemented my_strlen function


# git show <SHA-1 commit ID>: view the commit details
$ git show cbe1249b140dad24b2c35b15cc7e26a6f02d2277

# git diff: review the uncommited changes
$ git diff

# git commit --amend: correct last commit and creates a new commit ID
$ git commit --amend -m "Changed return type of my_strlen to size_t"
```

## Stash Operation
A note taking from [Git - Stash Operation](https://www.tutorialspoint.com/git/git_stash_operation.htm)

Stash operation takes the modified tracked files, stages changes, and saves them on a stack of unfinished changes that can  be reapplied at any time.

```
$ git stash
Saved working directory and index state WIP on master: e86f062 Added my_strcpy function
HEAD is now at e86f062 Added my_strcpy function

$ git stash list
stash@{0}: WIP on master: e86f062 Added my_strcpy function

# git stash pop: remove the changes from the stack and place them in the current working directory
$ git stash pop
```

## Fix Mistakes
A note taking from [Git - Fix Mistakes](https://www.tutorialspoint.com/git/git_fix_mistakes.htm)

### git checkout: Revert Uncommitted Changes
git checkout <file-name>: discard all the uncommit changes / recover committed and deleted files

### git reset: Move HEAD Pointer
#### soft reset
Use Git reset command with `--soft` option followed by commit ID, then it will reset the HEAD pointer without destroying anything.

```
# reset the HEAD pointer back by one position
$ git reset --soft HEAD~
```

#### mixed reset: default mode
Git reset with `--mixed` option reverts those changes from the staging area that have not been committed yet.
The actual changes made to the working copy of the file are unaffected.

#### hard
Clear the staging area, reset the HEAD pointer to the specific commit ID and delete the local file changes.

## Tag Operation
A note taking from [Git - Tag Operation](https://www.tutorialspoint.com/git/git_tag_operation.htm)

### Create Tags
```
$ git tag -a 'Release_1_0' -m 'Tagged basic string operation code' HEAD
```

### View Tags
```
$ git tag -l
Release_1_0

$ git show Release_1_0
```

### Delete Tags
```
$ git tag -d Release_1_0
Deleted tag 'Release_1_0' (was 0f81ff4)
# Remove tag from remote repository.
```

## Patch
A note taking from [Git - Patch](https://www.tutorialspoint.com/git/git_patch_operation.htm)

Patch is a text file, whose contents are similar to Git diff, but also has metadata about commits. (eg. commit ID, date, commit message) 

### Create Patches
```
$ git format-patch <COMMIT-ID>
$ git diff > diff.patch
$ git diff HEAD~1 > head_diff.patch
```

### Apply Patches
```
$ git apply <Patch-File-Name>
$ git am <Patch-File-Name>
```

FYR: [Git - Patch作成＆適用する](https://codechacha.com/ja/git-patch/), [Git でパッチファイルを作成する](https://maku77.github.io/git/git-patch.html)

## Managing Branches
A note taking from [Git - Managing Branches](https://www.tutorialspoint.com/git/git_managing_branches.htm)

```
# Create Branch
$ git branch new_branch

# Show Branches
$ git branch
* master
new_branch

# Switch Branches
$ git checkout new_branch
Switched to branch 'new_branch'

$ git branch
master
* new_branch

# Shortcut to Create and Switch Branch
$ git checkout -b test_branch
Switched to a new branch 'test_branch'

# Delete a Branch
$ git branch -D test_branch
Deleted branch test_branch (was 5776472).

# Rename a Branch
$ git branch -m new_branch wchar_support

# Merge a Branch
$ git merge wchar_support
Merge the branch wchar_support to the working branch
```

### Rebase Branches
> rebase コマンドを一言で言い表せば「指定したコミットを、ブランチを変えて作り直したり、ひとまとめにしたりして、ログを綺麗にするコマンド」です。
> [これで完璧! 図解でわかるgit rebaseの2つの使い方!](https://www.sejuku.net/blog/71919)

### Handling Conflicts
Skipped: [Git - Handling Conflicts](https://www.tutorialspoint.com/git/git_handling_conflicts.htm)
