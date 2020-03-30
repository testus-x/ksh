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
The `--set-upstream` is optional - it tells git, where a push per default gets sent to and from where to pull data per default. When you rebase often or somehow change the history of your branch (e.g. via `git rebase ...`) forcing a push to your origin is OK, because usually only very few people have it checked out/working on it and you are able to/should give them a hint via the related issue page on github. So to force a push, you may use:
```
git push --force-with-lease origin fix_problem
```

Now you can use the [**New pull request**](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork) button in the WebUI of your **origin** to create new Pull request. This will notify PR reviewers, which will have a look at it, may ask for changes or explainations and finally the PR gets merged to the related upstream branch. After this you may do some local housekeeping and delete the merged branch, e.g. using
```
git checkout master
git branch -d fix_problem
```


## Code [review](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-request-reviews) (collaborators)
Before a PR is allowed to be merged into a protected branch, at least two collaborators must [review](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/reviewing-proposed-changes-in-a-pull-request) and [approve](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/approving-a-pull-request-with-required-reviews) it (when done, the WebUI button becomes green). Furthermore, if Continuous Integration (CI) has been set up, all tests need to pass (green or yellow) unless there are very good/strong reasons, which should allow merging w/o CI passing.

All fixes should have a test case which demonstrates the defect. The test should fail before the change, and pass after the change. If it is too hard or too complex to create a test, at least the corresponding issue should have a clear description, how to re-produce the problem. The code should be documented inline as well unless it doesn't make sense or is more or less bloat.

## Merge PR to upstream (gatekeepers)
Only gatekeepers are able to merge PRs to protected upstream branches (e.g. `master`, `release`, `beta`, `fcs`). But

**NOTE**: Please do **not** use github's WebUI to merge PRs!

GitHub's green merge button on the WebUI should not be used to merge PRs because:
  * The "Create a merge commit" method will add an unnecessary merge commit.
  * The "Squash and merge" method will add metadata (the pull request #) to the commit title, which might be undesired (because of auto-closing the related issue). If more than one author contributes to the pull request, squashing only keeps one author (we append them at the end of the commit message using the `Co-authored-by: Given Name <email>`).
  * The "Rebase and merge" method has no way of adding metadata to the commit (like `Co-authored-by: ...`).

Therefore we **use our own local repository** to merge and push.

To be able to pull branches from other repos easily, one should add them on demand, but at least `upstream`, e.g. using:
```
git remote add upstream git@github.com:ksh-community/ksh.git
git remote add jghub git@github.com:*collaborator*/ksh.git
git remote -v
```
Note that **upstream** is now assigned to the official ksh repository on `https://github.com/ksh-community/ksh` and **jghub** to `git@github.com:*collaborator*/ksh.git`.

###	1. Sync with upstream
Clear any `am`/`rebase` that may already be underway and sync with the upstream. This should be actually done by every ksh developer before he creates a new branch and before he rebases his branch in turn to create a PR from it:
```
git am --abort
git rebase --abort
git checkout master
git fetch upstream
git merge --ff-only upstream/master
git push origin master
```
If the checkout above does not work because of pending changes, just stash them away using `git stash` and when done with merging, get them back using `git stash pop`.

###	2. Pull the PR into a new local branch
First, one should create a local branch, where the PR changes get merged in. Actually one may merge the changes directly into the master, however, usually one wanna **review** and **test** the changes, what sometimes takes a little bit longer or need to be postponed for this or that reason and thus would block any progress on the master or on your own branches. So with a new branch one may cherry pick, reword, edit, squash, join and drop commits, make other fine grained adjustments before the PR gets merged into the related upstream branch and if anything goes really wrong, one always has the option to simply drop the branch/changes made so far and start over again.

To find out, what to merge into the new branch, one can go to the [ksh repository](https://github.com/ksh-community/ksh) page, click on the [Pull requests](https://github.com/ksh-community/ksh/pulls) tab, and select the desired PR - you will end up with an URL like `https://github.com/ksh-community/ksh/pull/123` - NOTE: the number at the end of the URL is the github assigned **ID** of the PR. On the PR page click on the [command line instructions](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/checking-out-pull-requests-locally) link right beside the green `Squash and merge` button. The pattern you will see is basically `git pull git://github.com/*collaborator*/ksh.git *branch*`. So for our example:
```
# Option #1
git checkout master
git checkout -b fixXY
git pull jghub fix1
```
Here we can use `jghub` because we have added a remote repository and assigned this name to it. One would probably choose this option, when it is known, that the PR still needs some work and you have commit access to the related repository/branch. Each time the fix1 branch receives a new commit via push, it gets automatically propagated to the related PR by github.

Another option to pull in the PR is to download the PR as an mbox formatted file. It contains every commit in a separate e-mail with the commit formatted as a git patch. First we download it, than create a new branch based on the head of the master, apply it and remove the downloaded mbox file. E.g.:
```
# Option #2
wget -O /tmp/prXY https://github.com/ksh-community/ksh/pull/123.patch
git checkout master
git checkout -b fixXY
git am --whitespace=fix /tmp/prXY
rm /tmp/prXY
```
Wrt. wget you may simply copy and paste the URL of the PR page and append `.patch` to it (`${URL}`**.patch**). The advantage here is, that one may change the Author and e-mail address by just editing the `From:` values in the mbox file (mbox == plain text). Furthermore one may use an e-mail client to browse all commits and determine the number of commits in the PR very easy (e.g. `mutt -f /tmp/prXY` or `grep ^Subject /tmp/prXY`). Because the commits get re-created, they will have a different hash, than in the original branch and PR.

Last but not least there is a 3rd option: [pull the PR directly into a new branch](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/checking-out-pull-requests-locally#modifying-an-inactive-pull-request-locally). E.g.:
```
# Option #3
git checkout master
git fetch upstream pull/123/head:fixXY
git checkout fixXY
```
That's probably the most convinient option and keeps commit hashes as is.

###	3. Adjust commits 

In the new local branch, which contains the PR, one should now modify all commits as needed. The following **rules of thumb** should be taken into account:
  * use the **issue page** on github to discuss and track PR related ideas, enhancements, tasks, bugs, brainstorming (the **what**). Document the results as compact as possible in the code or commit message.
  * use the **PR page** on github to discuss/annotate code related details (the **how**)
  * Commit messages: title
    * max. 72 chars. If too long, try to shorten it, add a <LF> and use the longer description as "body" of the commit.
    * should contain a reference to the issue ID, e.g. see #321
    * to auto-close the related issue prefix with 'fix #123' or append '(fixes #321)' or use any other [auto-close phrase]()
  * Commit message: body
    * the commit message body gets separated from the commit message title by a single blank line.
    * if the message title contains no reference to the issue ID, add the following tag to the body of all commits: `  See: #321` - replace 321 with the issue ID. You may add more using a single whitespace as separator.
    * max. line length: 80 chars
    * if more than one author should be named, add the following line for each of them to the end of the message body: `Co-authored-by: Given Name <email>` - the email should be the one, which the author used to registred with github. 
    * important know how refs should be part of the commit message unless they are already inlined in the code. If github issue pages are not available, one should still be able to get the know how to understand the patch pretty easy.
  * Code units, commits, patches: One should be able to read and understand them pretty easy, quickly. So:
    * keep them as compact as possible
    * inline documentation where it makes sense
    * squash as needed - especially if several changes are made to a PR it might be very cluttered and hard to catch the real changes to the code base. Squashing may help to selectively clean up the mess.
    * trim trailing whitespaces. If you have a color enabled terminal, use `git diff master` and watch out for red boxes.

To fix any of the of the problems mentioned above, one should use the **rebase** feature of git:
```
git rebase -i master
# or 
git rebase -i HEAD~${number_of_commits_in_the_PR}
```

The option **-i** is important!

```
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
```

###	4. Merge into the master and push to upstream + origin
```
git checkout master
git merge --ff fixXY
git push upstream master
git push origin master
```
