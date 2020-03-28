# Pull Request (PR) management

## Clone the clone
Before you start working on a fix or feature, you should clone the [**ksh**](https://github.com/ksh-community/ksh) repository. You can do that by cloning the repository directly. However, to be able to submit PRs you need to have your own **ksh** clone on github.com. Clicking the [**Fork**](https://help.github.com/en/github/getting-started-with-github/fork-a-repo) button on the upper right accomplishes this. It redirects you to your ksh clone: here we refer to it using the name **origin**. Once you have commited any changes to this repository, you can use the [**New pull request**](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork) button to create new Pull request.

However, you probabily do not wanna edit files remotely (via Github WebUI) and you want/should test your changes on your preferred machine and OS. So you should clone your `origin` to this machine/OS - here we refer to it using the term **local**. Via CLI it is as easy as:
```
git clone git@github.com:*your_account*/ksh.git
```

## Issue, branch, comit and create a PR
Before you start working on an problem or feature, please check the [ksh Issue](https://github.com/ksh-community/ksh/issues) web page, whether there is not already work in progress (WIP) to avoid wasting your valueable time. If there is someone already working on it, please check, whether it is possible to work together on it. If you can't find an Issue for your problem/feature, please [create a new Issue](https://github.com/ksh-community/ksh/issues/new) and refer to it using hash + number (e.g. #123) when you refer to it on github. Within the issue web page you may ask for further guidance or get some hints from other collaborators.

Before you actually start changing any code, you should always make sure, that your master is up-to-date (see below **1. Sync with upstream**) 



## Code [review](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-request-reviews)
Before a PR is allowed to be merged into a protected branch, at least two collaborators must approve it (green). Furthermore, if Continuous Integration (CI) has been set up, all tests need to pass (green or yellow) unless there are very good/strong reasons, which should allow merging w/o CI passing.

All fixes should have a test case which demonstrates the defect. The test should fail before the change, and pass after the change. If it is too hard or too complex to create a test, at least the corresponding issue should have a clear description, how to re-produce the problem. The code should be documented inline as well unless it doesn't make sense or is more or less bloat.

## Do **not** use github WebUI to merge PRs
GitHub's green merge button on the WebUI should not be used to merge PRs. Reasons are:
    * The "Create a merge commit" method will add an unnecessary merge commit.
        * The "Squash and merge" method will add metadata (the pull request #) to the commit title. If more than one author contributes to the pull request, squashing only keeps one author.
    * The "Rebase and merge" method has no way of adding metadata to the commit.
Therefore we use our own local repository to merge and push back.

## Setting up his own local repo
Since a gatekeeper is usually a developer as well, one does not need to setup a separate local repository just for upstream merging, but may use his own via github WebUI forked repository. The basic rule of thumb here is: **Never merge any of your own changes/branches into a protected branch!** Only PR related patches/branches (which might be submitted to the upstream by creating a PR) are allowed to be merged into the related protected branch.

So to setup, i.e. clone your via github WebUI forked repository, do:
```
git clone git@github.com:*my_account*/ksh.git
cd ksh
# optional
git config user.email 'email+address@to.use.4this.repo'
git config user.name 'Given to.use.4this.repo Surname'
```
Note that git automatically assigns **origin** to your repository on https://github.com/*my_account*/ksh/. Therefore you may push you own local branches to your github repository via `git push --set-upstream origin my-branch`. The `--set-upstream` is optional - tells git, where a push per default gets sent to and from which repository to pull data per default.

If there are known repos you often use to pull branches from, add them on demand or right now:
```
git remote add upstream git@github.com:ksh-community/ksh.git
git remote add *collaborator* git@github.com:*collaborator*/ksh.git
```
Note that **upstream** is now assigned to the official ksh repository on https://github.com/ksh-community/ksh/.


## Merge the PR
In the following example the **master** branch is the target of PR merge.

### 1. Sync with upstream
Clear any `am`/`rebase` that may already be underway and sync with the upstream. This should be actually done by every ksh developer before he creates a new branch and before he rebases his branch, before creating a PR from it:
```
git am --abort
git rebase --abort
git checkout master
git fetch upstream
git merge --ff-only upstream/master
git push master
```
If the checkout above does not work because of pending changes, just stash them away using `git stash` and when done with merging, get them back using `git stash pop`.

### 2. Create a new local branch and merge in the PR
A gatekeeper may omit this step and merge the changes directly into the master, however, usually you wanna review and test the changes, which sometimes take a little bit longer or need to be postponed for this or that reason. So creating a new local branch makes you much more flexible:
```
git checkout -b fixXY
git pull jghub fix1
```
Now you can modify all commits as needed (and remove all changes being made so for using `git clean -f -d`).

TBD: reword + include issue NR, rebase

### 3. Merge into the master and push to upstream and origin
git co master
git merge --ff fixXY
git push upstream master
git push origin master
#   

