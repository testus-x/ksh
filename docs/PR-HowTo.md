# Pull Request (PR) / Workflow HowTo

## Clone the clone
Before you start working on a fix or feature, you should clone the [**ksh repository**](https://github.com/ksh-community/ksh) - here we refer to it using the term **upstream**. You can do that by cloning the repository suing git directly. However, to be able to create and submit PRs you need to have your own **ksh** clone on github.com. Clicking the [**Fork**](https://help.github.com/en/github/getting-started-with-github/fork-a-repo) button on the upper right of the [**ksh repository**](https://github.com/ksh-community/ksh) accomplishes this. As soon as finished it redirects you to your ksh clone: here we refer to it using the term **origin**.

However, you probably do not wanna edit files remotely (via Github WebUI) and you want/should test your changes on your preferred machine and OS. So you should clone your `origin` to this machine/OS - here we refer to this new clone using the term **local**. Via CLI it is as easy as:
```
git clone git@github.com:*my_account*/ksh.git
# optional
cd ksh
git config user.email 'email+address@to.use.4this.repo'
git config user.name 'Given to.use.4this.repo Surname'
```
Note that git automatically assigns the name **origin** to the cloned repository (`https://github.com/*my_account*/ksh/`) and can be used to refer to it within git commands.

## Create an Issue
Before you start working on an problem or feature, please check the [ksh Issue](https://github.com/ksh-community/ksh/issues) web page, whether there is not already work in progress (WIP) to avoid wasting your valueable time. If there is someone already working on it, please check, whether it is possible to work together. If you can't find an Issue for your problem/feature, please [create a new Issue](https://github.com/ksh-community/ksh/issues/new) and refer to it using a number sign + number (e.g. #123) when you refer to it on github. Within the issue web page you may ask for further guidance or get some hints from other collaborators.

## Branch, commit, rebase to master
Before you actually start changing any code, you should always make sure, that your master is up-to-date - see below [**Sync with upstream**](#user-content-1-sync-with-upstream) for the related CLI commands. 

When your master is up-to-date, create a new branch, e.g. named `fix_problem`:
```
git checkout master
git checkout -b fix_problem
```
Always make sure, that you are on the right and _not_ on the master branch, before you start working, e.g. using `git branch`. Each time you think you made progress which should not be lost, commit it to your local branch. Finally, when you think you are done (what might be some days/weeks later), make sure your local master is still in [sync with the upstream master](#user-content-1-sync-with-upstream) and rebase your branch to the master's HEAD, e.g. using:
```
git checkout fix_problem
git rebase master
```
Depending on what you do and what's going on in the upstream master, it is probably a good idea to sync and rebase often, so that you can be sure, that you do not double any work, that your changes match the current code base and the SW still works as expected.

## Create a PR
Once you are at the point you think you are in sync with the upstream master and the work is finished or someone should have a look at it, push the branch to your **origin**, e.g. using:
```
git checkout fix_problem
git push --set-upstream origin fix_problem
```
The `--set-upstream` is optional - it tells git, where a push per default gets sent to and from where to pull data per default.

Now you can use the [**New pull request**](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork) button in the WebUI of your **origin** to create new Pull request. This will notify PR reviewers, which will have a look at it, may ask for changes or explainations and finally the PR gets merged to the related upstream branch. After this you may do some local housekeeping and delete the merged branch, e.g. using
```
git checkout master
git branch -d fix_problem
```


## Code [review](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-request-reviews) (collaborators)
Before a PR is allowed to be merged into a protected branch, at least two collaborators must approve it (when done, the WebUI button becomes green). Furthermore, if Continuous Integration (CI) has been set up, all tests need to pass (green or yellow) unless there are very good/strong reasons, which should allow merging w/o CI passing.

All fixes should have a test case which demonstrates the defect. The test should fail before the change, and pass after the change. If it is too hard or too complex to create a test, at least the corresponding issue should have a clear description, how to re-produce the problem. The code should be documented inline as well unless it doesn't make sense or is more or less bloat.

## Merge PR to upstream (gatekeepers)
Only gatekeepers are able to merge PRs to protected upstream branches (e.g. `master`, `release`, `beta`, `fcs`). But

**NOTE**: Please do **not** use github's WebUI to merge PRs!

GitHub's green merge button on the WebUI should not be used to merge PRs because:
  * The "Create a merge commit" method will add an unnecessary merge commit.
  * The "Squash and merge" method will add metadata (the pull request #) to the commit title. If more than one author contributes to the pull request, squashing only keeps one author.
  * The "Rebase and merge" method has no way of adding metadata to the commit.
Therefore we use our own local repository to merge and push back.

Because a gatekeeper is usually a developer as well and usually has its own local clone already in place he can use this repository. To be able to pull branches from other repos (remotes including `upstream`), one should add them on demand (but at least upstream), e.g. using:
```
git remote add upstream git@github.com:ksh-community/ksh.git
git remote add *collaborator* git@github.com:*collaborator*/ksh.git
git remote -v
```
Note that **upstream** is now assigned to the official ksh repository on https://github.com/ksh-community/ksh/.

###	1. Sync with upstream
Clear any `am`/`rebase` that may already be underway and sync with the upstream. This should be actually done by every ksh developer before he creates a new branch and before he rebases his branch in turn to create a PR from it:
```
git am --abort
git rebase --abort
git checkout master
git fetch upstream
git merge --ff-only upstream/master
git push master
```
If the checkout above does not work because of pending changes, just stash them away using `git stash` and when done with merging, get them back using `git stash pop`.

###	2. Create a new local branch and merge in the PR
A gatekeeper may omit this step and merge the changes directly into the master, however, usually you wanna **review** and **test** the changes, what sometimes takes a little bit longer or need to be postponed for this or that reason. So creating a new local branch makes provides much more flexibility:
```
git checkout -b fixXY
git pull jghub fix1
```
Now you can modify all commits as needed (and remove all changes being made so for using `git clean -f -d`). 

TBD: reword + include issue NR, rebase

###	3. Merge into the master and push to upstream and origin
```
git checkout master
git merge --ff fixXY
git push upstream master
git push origin master
```
