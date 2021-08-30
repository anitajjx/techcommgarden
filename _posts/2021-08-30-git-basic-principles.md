---
title: "Basic principles of Git"
categories: Git
permalink: git-principles.html
tags: [Git]
Summary: Understand some basic principles/concepts to know how Git works. 
---
## What is Git

Git is an open source distributed version control system, as a contrast to other version control systems like CVS, Subversion that are centralized. Below are the differences between centralized and distributed version control systems.

      {% include image.html file="blog_imgs/git_centralized_distributed.png" caption="Centralized version control system vs Distributed version control system" %}

## How Git organize data

Git organize data with following ideas:
      - Organize data into four States
      - Git's database structure: tree -> leaves(commits) -> file/fileset

### Git's four States

Git organize data into a four States - Working directory, Staging area, Local Git repository, and Remote Git repository. The first three States live on your computer, allowing you to work on the project autonomously. This means you can add, commit code to the repo even when offline.

      {% include image.html file="blog_imgs/git_states.png" caption="Git's four States" %}

### Git database

When you create local repo with `git clone` or `git init`, Git initializes its database and saves it in a hidden directory called **.git**:

```bash
$ tree -L 1 .git
.git
├── branches
├── COMMIT_EDITMSG
├── config
├── description
├── HEAD
├── hooks
├── index
├── info
├── logs
├── objects
├── packed-refs
└── refs

$ tree -L 2 .git/refs/
.git/refs/
├── heads
│   ├── ax_works
│   └── main
├── remotes
│   └── origin
└── tags
```

The most interesting subdirectories are:

- **HEAD**: Contains the path to the reference that indicates the current branch
- **config**: The repo configuration file
- **objects**: Contains all the repo files. The file content is encoded and compressed
- **refs/heads**:  Contains a file per branch. Each file is named after a branch, and its content is the SHA1 of the last commit

### Branch and Tag

Git repos are organized with tags and branches. **Branch** is one part of Git Tree, permitting user to work independently with another branch. It is actually a movable pointer, that can move forward or backward commit object in Git tree. **Tag** is a pointer to a certain commit, and just an easier way to reference it than memorizing hash numbers. A common way to use tags is for version numbering.

      {% include image.html file="blog_imgs/git_example_branch_structure.png" caption="Example branch structure in Git" %}

- **master** branch: Where some development happens currently points to snapshot F.
- **old** branch: Marks an older snapshot, perhaps a possible fix development point.
- **feature** branch: Has other changes for a specific feature. Change sets are noted as going from one version to another, for example "[B->D]"
- **Tag**: Commit B has also been tagged as fixing bug number 123.

In this blog, I introduced some basic concepts and principles of Git, hopefully you get a bit clearer about how Git works. I will walk through some workflows and operations often used in daily work. 