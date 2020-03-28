# Pull Request (PR) management

## Clone the repo
Before you start working on a fix or feature, you should clone the [**ksh**](https://github.com/ksh-community/ksh) repository. You can do that by cloning the repository directly. However, to be able to submit PRs you need to have your own **ksh** clone on github.com. Clicking the [**Fork**](https://help.github.com/en/github/getting-started-with-github/fork-a-repo) button on the upper right accomplishes this. Once you have done it, you get redirected to your ksh clone, which we call here **origin**. Once you have commited any changes to this repository, you can use the [**New pull request**](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/creating-a-pull-request-from-a-fork) button to create new Pull request.

However, you probabily do not wanna edit files remotely (via Github WebUI) and y
ou want/should test your changes on your preferred machine and OS. So you should
 clone your `origin` to this machine/OS - here we refer to it as **local** repos
itory. Via CLI you it is as easy as:
```
git clone git@github.com:*owner*/ksh.git
```

## Code [review](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-request-reviews)
Before a PR is allowed to be merged into a protected branch, at least two collaborators must approve it (green). Furthermore, if Continuous Integration (CI) has been set up, all tests need to pass (green or yellow) unless there are very good/strong reasons, which should allow merging w/o CI passing.

All fixes should have a test case which demonstrates the defect. The test should fail before the change, and pass after the change. If it is too hard or too complex to create a test, at least the corresponding issue should have a clear description, how to re-produce the problem. The code should be documented inline as well unless it doesn't make sense or is more or less bloat.

