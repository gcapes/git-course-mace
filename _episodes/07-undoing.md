---
title: "Undoing changes"
teaching: 25
exercises: 0
questions:
- "How can I discard unstaged changes?"
- "How do I edit the last commit?"
- "How can I undo a commit?"
objectives:
- "Be able to discard unstaged changes"
- "Be able to amend the most recent commit"
- "Be able to discard all changes since a particular commit"
- "Be able to undo the changes introduced by a commit"
keypoints:
- "`git checkout <file>` discards unstaged changes"
- "`git commit --amend` allows you to edit the last commit"
- "`git revert` undoes a commit, preserving history"
- "`git reset` undoes a commit by deleting history"
---

There are a number of things which we can amend and change after they have been
commited in Git.

### Discarding local changes

Maybe we made our change just to see how something looks, or to
quickly try something out. But we may be unhappy with our changes.
Let's try this by deleting all the lines in the `temperature_conversions.m` script,
and then saving the file.
We haven't get done a `git add` so we can just throw the changes away and return
our file to the most recent version we committed to the repository by using:

```
$ git checkout temperature_conversions.m	# Discard edits we just made
```
{: .language-bash}

If we now open our file again (in MATLAB or a text editor),
we can see the contents have been restored to the most up-to-date version in the repository:

```
$ git status					# See that we have a clean working directory
$ notepad temperature_conversions.m		# Inspect file to verify changes have been discarded
```
{: .language-bash}

---

### Amending the most recent commit

If you just made a commit and realised that either you did it a bit too early
and the files are not yet ready to be commited,
or your commit message is not as it is supposed to be,
you can overwrite the last commit using the command `$ git commit --amend`.

This opens up the default editor for Git which includes the previous commit
message - you can edit it and close the editor. This will simply fix the commit
message.

But what if we want to change the contents of the commit
(maybe we want to edit our last set of changes,
or forgot to include some files in the commit)?

Let's try it on our example. First, we'll add a comment above our conversions,
but with a typo:

```
...
% Save degree symbol as a variable
deg = char(176);

% Check some key conversion
disp(['Water boils at 100', deg, 'C, which is ', num2str(celsius_to_fahrenheit(100)), deg, 'F.'])
...
```
{: .language-matlab}
	
Let's then add and commit `temperature_conversions.m`.

```
$ git add temperature_conversions.m
$ git commit -m "Comment key conversions section"
```
{: .language-bash}
	

Run `git log -3 --oneline` and look at the latest commit message and ID.

```
9a2036b Comment key conversions section
0377892 Merge branch 'newton'
39ba929 Add help text for script
```
{: .output}

Now, we want to fix our commit by correcting the typo.
The corrected line should be as follows:

```
% Check some key temperature converisons
```
{: .language-matlab}

Correct the typo, then stage the file

```
$ git add temperature_conversions.m	# Add reference file
$ git commit --amend			# Amend most recent commit
```
{: .language-bash}

This will again bring up the editor and we can amend the commit message if required.

Now when we run `git status` and then `git log` we can see that our Working
Directory is clean and that the most recent commit ID has changed
because of the new contents of the commit.

```
$ git status
$ git log -3 --oneline
```
{: .language-bash}

```
3dd68cc Comment key conversions section
0377892 Merge branch 'newton'
39ba929 Add help text for script
```
{: .output}

---

### `git revert` (undo changes associated with a commit)

`git revert` removes the changes applied in a specified commit. However, rather 
than deleting the commit from history, git works out how to undo those changes
introduced by the commit, and appends a new commit with the resulting content.

Let's try it on our example.
We change our mind and decide that the last comment wasn't really required.


```	
$ git revert HEAD		# Undo changes introduced by most recent commit
```
{: .language-bash}

When we revert, a new commit is created. The *HEAD* pointer and the branch
pointer are in fact moved forward rather than backwards. 	
	
We can revert any previous commit. That is, we can "abandon" any of the
previous changes. However, depending on the changes we made, we may bump into
a *conflict* (which we will cover in more detail further on). 

```
error: could not revert 3dd68cc... Comment key conversions section
hint: after resolving the conflicts, mark the corrected paths
hint: with 'git add <paths>' or 'git rm <paths>'
hint: and commit the result with 'git commit'
```
{: .output}
	
Behind the scenes Git gets confused trying to merge the commit *HEAD* is pointing
to with the past commit we're reverting. 

So we have seen that `git revert` is a non-destructive way to undo a commit.
What if we don't want to keep a record of undoing commits? That would give a neater
history. `git reset` can also be used to undo commits, but it does so by deleting
history.

---

### `git reset --hard` (restore a previous state by deleting history)

`git reset` has several uses, and is most often used to unstage files from the staging
area i.e. `git reset` or `git reset <file>`.

We are going to use a variant `git reset --hard COMMIT_ID` to restore things to how 
they were at `COMMIT_ID`. This is a permanent undo which deletes all changes more recent
than `COMMIT_ID` from your history. There is clearly potential here to lose work, so use
this command with care.

Let's try that on our script, using the same example as before. Now we have two commits
which we want to abandon: the commit adding a comment, and
the subsequent revert commit. We can achieve this by resetting to the last
commit we want to keep.

We can do that by running:

```
$ git reset --hard HEAD^^	# Move tip of branch to two commits before HEAD
```
{: .language-bash}
```
HEAD is now at 0377892 Merge branch 'newton'

```
{: .output}

This moves the tip of the branch back to the specified commit. If we look in-depth, 
this command moves back two pointers: `HEAD` and the pointer to the tip of the
branch we currently are working on (master). (`HEAD^` = the commit right before HEAD;
`HEAD^^` = two commits before HEAD)

The final effect is what we need: we abandoned the commits and we are now back
to where we were before making the commit with the comment we are not keeping.

Feel free to verify this by checking the contents of the `temperature_conversions.m` file.

---
Click for an [animation] of the revert and reset operations we just used.

[animation]: https://learngitbranching.js.org/?NODEMO&command=git%20commit;git%20revert%20C2;git%20reset%20HEAD~2

[This article](https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified) discusses more in
depth `git reset` showing the differences between the three options:

* `--soft`
* `--mixed`
* `--hard`


> ## Top tip: do not use `git reset` with remote branches 
> There is one important thing to remember about the `reset` command - it
> should only be used with branches that have not been shared yet (that is they
> haven't been pushed into a remote repository that others are using). Resetting
> is changing the history **without** leaving trace. This is always a bad practice
> when using remote repositories and can lead to a horrible mess.
> 
> Reverting records the fact of "abandoning the commit" in the history.
> When we revert in a branch that is shared with others and then push that branch
> into the remote repository, it is as if we "came clean" about what we were
> doing. Everyone who pulls the branch in which we reverted changes will see it.
> With `git reset` we "keep it secret" that we have undone some changes.
> 
> As such, if we want to abandon changes in branches that are shared
> with others, we should to use the `revert` command.
{: .callout}

![Reset vs revert](../fig/git-revert-vs-reset.svg)


See this [Atlassian online tutorial](
https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting/commit-level-operations)
for further reading about the differences between `git revert` and `git reset`.

### How to undo almost anything with Git
See [this blog post](https://github.com/blog/2019-how-to-undo-almost-anything-with-git) for more example scenarios and how to recover from them.
