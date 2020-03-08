---
layout: episode
title: Working with remotes, distributed version control
teaching: 15
exercises: 0
questions:
  - How can we share repositories with others?
  - How can we keep repositories in sync?
objectives:
  - Understand different workflows within the distributed version control.
keypoints:
  - "`git clone` copies everything and sets some pointers to remember where the clone came from."
  - "`origin` is just an alias."
  - We can add and remove remotes.
  - We can call these aliases as we like.
  - We synchronize remotes via the local clone.
  - "To see all remotes use `git remote -v`."
---


# Situation

- Someone has given you access to a repository online and you want to contribute to it.
- You would like others to contribute to your online repository.


# Basics

We already discussed some basics of working with remotes in [this lesson](../08-remotes).

* Technically, a **remote** repository is only used as a collaboration point, it’s just the Git data.
* Git can use different protocols to transfer data: Local, HTTP, SSH and Git (more on that [here](https://git-scm.com/book/en/v2/Git-on-the-Server-The-Protocols)).


## Cloning repositories

The `git clone` command makes a copy of a repository.  For example,

```shell
$ git clone https://host.com/user/project.git project
```

- Contributing to a repository often starts by cloning the entire repository.
- By cloning we clone all commits, all branches and tags, **entire history**.
- **A clone is a full-fledged repository.**

---

## Synchronizing changes between repositories

- We need a mechanism to communicate changes between the repositories.
- We will **pull** or **fetch** updates **from** remote repositories (we will discuss the difference between pull and fetch).
- We will **push** updates **to** remote repositories.

---

# Distributed version control


Git implements a **distributed** version control.

This means that any type of repository links that you can think of can be
implemented - not just "everything connects to one central server.

So every developer can do both:
* contribute code to other repositories
* maintain a public repository on which others can base their work and which they can contribute to. 

Two topologies are very frequent: centralized and forking layout.


### Centralized layout

![]({{ site.baseurl }}/img/git-collaborative/forking/centralized.svg)

In Git, **all repositories are equivalent** but in the typical **centralized** style, we consider one repository
as the main development line and this is marked as "central".
The "central" is a role, not a technical difference.

Features:

- Typically all developers have both read and write permissions (double-headed arrows).
- Suited for cases where all developers are in the same group or organization etc.
- Code review workflow is possible.
- Code review can be coupled with with automated testing.

Advantages:

- More familiar for Subversion or CVS users.
- Easier: for each clone there is only one remote.

Disadvantages:

- Everybody who wants to contribute needs write access.
- Maintainer needs to trust the developers to not break things (but you can protect branches).

Real life examples:

- [https://github.com/ropensci/plotly](https://github.com/ropensci/plotly)


### Forking layout

![]({{ site.baseurl }}/img/git-collaborative/forking/forking-overview.svg)

In the **forking layout**, again we call one repository the "central"
repository but people push to **forks** (their own copies of the
repository on Github).

Features:

- Most developers have only read access to the main project.
- For a public repository everybody has read access.
- Only very few people (the maintainers) have write access.
- Typically nobody pushes directly to the central repo.
- Code review can be coupled with with automated testing.
- Central repo and the forks typically reside in the "cloud".
- Naturally grows out the centralized model once more people begin
  contributing.

Advantages:

- Code is integrated via code review (during pull/merge request).
- Maintainer has full control over what goes in.
- Allows contributions from people you don't know yet (in practice not possible in centralized layout).
- Structurally helps to implement peer review in coding (code review).

Disadvantages:

- Learning curve: we need to deal at least with two remotes (fork and central repo).
- Introduces additional steps (to e.g. update the fork).

Real life examples:

- NumPy: [https://numpy.org/devdocs/dev/index.html](https://numpy.org/devdocs/dev/index.html)
- [https://github.com/jupyterlab/jupyterlab](https://github.com/jupyterlab/jupyterlab)

---


## More on remotes:

- In [this lesson](../08-remotes) we learnt how to set the remote's url and how to change it:

```shell
$ git remote add origin <git-repo-url>
$ git remote set-url origin <new-git-url>
```

- There is nothing special about the name `origin`. The `origin` is just an alias.
- We can call these aliases as we like.
- We can have multiple remotes.
- We can add and remove remotes:

```shell
$ git remote add upstream https://github.com/project/project.git
$ git remote rm upstream
$ git remote add group-repo https://example.com/exciting-project.git
$ git remote rm group-repo
```

We synchronize remotes via the local clone.

To see all remotes:

```shell
$ git remote --verbose
```


----------------------------------


# Centralized workflow exercise

In this exercise, we practice collaborative centralized workflow.
First, we all **clone** (make a local copy) and try to **push** (send
code to) the main repository.  We'll see a small problem with that,
and then make a **pull request** (sending code so that others can
review and accept later).  We'll discuss how this leads to code review
and discuss a number of typical pitfalls.


## Before we start

Everyone needs their GitHub account to be added to our central repository.

1. Participants add their usernames to a shared document.
2. Instructor adds participants as collaborators to this project.


## After participants have been added as collaborators


### 1. Clone [this repository](https://github.com/comp-sci-tools/centralized-workflow-exercise)

```
$ git clone https://github.com/comp-sci-tools/centralized-workflow-exercise centralized-workflow-exercise
```

Note: instead of using https you can also clone using ssh keys:
- [https://help.github.com/articles/connecting-to-github-with-ssh/](https://help.github.com/articles/connecting-to-github-with-ssh/)
- [https://docs.gitlab.com/ee/gitlab-basics/create-your-ssh-keys.html](https://docs.gitlab.com/ee/gitlab-basics/create-your-ssh-keys.html)


This is a representation of what happens when you clone:

*remote*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/01-remote.svg)

*local*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/01-local.svg)

- We clone the entire history, all branches, all commits.
- `git clone` creates pointers `origin/master` and `origin/experiment`.
- `origin` refers to where we cloned from, try: `git remote -v`.
- `origin` is a shortcut for the full URL.
- `origin/master` and `origin/experiment` are read-only pointers.
- They only move during `git pull` or `git fetch` or `git push`.
- Only `git pull` or `git fetch` or `git push` require network.
- All other operations are local operations.


### 2. Step into the newly created directory

```
$ cd centralized-workflow-exercise
```


### 3. Create a file with a unique name, e.g.: `yourusername.txt`

In this file share your favourite cooking recipe or haiku or Git trick or whatever.


### 4. Stage and commit the change

```
$ git add yourusername.txt
$ git commit
```

*remote*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/01-remote.svg)

*local*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/04-local.svg)


### 5. Try to push the change to the upstream repository

```
$ git push origin master
```

By **"upstream"** we mean here the repository which we have cloned.
Imagine "upstream" being closer to the main development and your local
clone to be "downstream".


### 6. **Stop here**: why push for most participants was rejected?

You probably see something like this:

```shell
$ git push
To https://github.com/user/repo.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'https://github.com/user/repo.git'
To prevent you from losing history, non-fast-forward updates were rejected
Merge the remote changes (e.g. 'git pull') before pushing again.  See the
'Note about fast-forwards' section of 'git push --help' for details.
```

The push only worked for one participant. Why?

*remote*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/06-remote.svg)

*local*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/04-local.svg)

The natural reflex is now to `git pull` first but
what happens if we `git pull origin master`?


### 7. Pull updates from the upstream repository

```
$ git pull origin master
```

*remote*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/06-remote.svg)

*local*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/07-local.svg)


### 8. **Stop here**: why we obtained a merge commit locally?

Ideas? What happened under the hood?

We did:

```shell
$ git pull origin master
```

which is equivalent to:

```shell
$ git fetch origin
$ git merge origin/master
```

- `git pull` consists of two operations: a `git fetch` followed by a `git merge`.
- Summary: `git pull origin master` fetches `master` from `origin` and merges it.
- There is always a `git merge` "hidden" in `git pull`.
- Many people will simply `git pull`, very careful people first `git fetch` and inspect the commits before merging them.
- With Git you typically merge several times a day without even noticing.

An alternative:

```shell
$ git pull --rebase origin master
```

would have produced:

*remote*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/06-remote.svg)

*local*: ![]({{ site.baseurl }}/img/git-collaborative/centralized/08-local.svg)


### 9. Try to push again

It will work for one more person.


### 10. Create a branch `yourname/somefeature` pointing at your commit

First find out the hash of your commit. You can do this using `git graph` or `git log`:

```
$ git log yourusername.txt
```

Then create a branch "in the past" pointing to that hash:

```
$ git branch yourname/somefeature [hash]
```

The `yourname/` prefix has no special meaning: it is just part of a
branch name to indicate who made it (we discussed branch naming before).


### 11. Push your change as a new branch

```
$ git push origin -u yourname/somefeature
```

Can we leave out the `-u` (`-u` is equivalent to `--set-upstream`)? 


### 12. Submit a pull request

Submit a pull request from your branch towards the `master` branch.
Do this through the web interface.

---

## How to make changes to remote branches

Create a local branch `somefeature` tracking `origin/somefeature`:

```shell
$ git checkout -b somefeature origin/somefeature
```

If there is no local branch `somefeature` and there is a remote branch `origin/somefeature`, then this is enough:

```shell
$ git checkout somefeature
```

Once we track a remote branch, we can pull from it and push to it:

```shell
$ git pull origin somefeature
$ git push origin somefeature
```

We can also delete remote branches:

```shell
$ git push origin --delete somefeature
```

---

## Centralized workflow with protected branches

- Forking workflow may be overkill for small closed-source projects.
- A good alternative to the forking workflow for a group of collaborators
  is to use a **protected** (only certain people may directly push to
  it) `master` branch.
- Only designated "code owners" have write access to the protected branch.
- Anyone else may contribute code changes via pull requests from feature branches.
- Pull requests need to be approved by a code owner.
- Discuss the advantages or disadvantages of this workflow.