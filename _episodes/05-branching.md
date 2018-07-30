---
title: "Branching"
teaching: 25
exercises: 15
questions:
- "What is a branch?"
- "How can I merge changes from another branch?"
objectives:
- "Know what branches are and why you would use them"
- "Understand how to merge branches"
- "Understand how to resolve conflicts during a merge"
keypoints:
- "`git branch` creates a new branch"
- "Use feature branches for new ideas and fixes, before merging into `master`"
- merging does not delete any branches
---

### What is a branch?

You might have noticed the term *branch* in status messages:

~~~
$ git status 
~~~
{: .bash}
~~~
On branch master 
nothing to commit (working directory clean)
~~~
{: .output}

and when we wanted to get back to our most recent version of the repository, we
used `git checkout master`.

Not only can our repository store the changes made to files and directories, it
can store multiple sets of these, which we can use and edit and update in
parallel. Each of these sets, or parallel instances, is termed a `branch` and
`master` is Git's default branch. 

A new branch can be created from any commit. Branches can also be *merged*
together. 

### Why are branches useful?
Suppose we've developed some software and now we want to
try out some new ideas but we're not sure yet whether we'll keep them. We
can then create a branch 'feature1' and keep our `master` branch clean. When
we're done developing the feature and we are sure that we want to include it
in our program, we can merge the feature branch with the `master` branch. 
This keeps all the work-in-progress separate from the `master` branch, which 
contains tested, working code.

When we merge our feature branch with master git creates a new commit which
contains merged files from master and feature1. After the merge we can continue
developing. **The merged branch is not deleted.** We can continue developing (and
making commits) in feature1 as well.

### Branching workflows

One popular model is the [Gitflow model](http://nvie.com/posts/a-successful-git-branching-model/):

- A `master` branch, representing a released version of the code
- A release branch, representing the beginnings of the next release - a branch 
where the code is still undergoing testing
- Various feature and/or developer-specific branches representing
work-in-progress, new features, bug fixes etc

For example:

![Feature branches ([image source)](https://www.atlassian.com/git/tutorials/comparing-workflows#feature-branch-workflow)](../fig/feature-branch.svg)

There are different possible workflows when using Git for code development. 
If you want to learn more about different workflows with Git, have a look at
[this discussion](https://www.atlassian.com/git/tutorials/comparing-workflows)
on the Atlassian website.


### Branching in practice

We have worked so far with the three most widely used temperature scales,
but after a little research it turns out these are far from the only temperature scales.
We decide to try out conversions from
[degrees Newton](https://en.wikipedia.org/wiki/Newton_scale) to Celsius.
We're just trying out this idea, so we'll create a new branch to work in.

~~~
$ git checkout -b newton
~~~
{: .bash}
~~~
Switched to a new branch 'newton'
~~~
{: .output}

We're going to add a function to convert from the Newton scale to Celsius,
and update our script to print out some values.
However, before we get started it's a good practice to check that we're working
on the right branch.

~~~
$ git branch			# Double check which branch we are working on
~~~
{: .bash}
~~~
  master
* newton
~~~
{: .output}

The * indicates which branch we're currently on.
Now let's write that function and update the script by adding an H1 comment
and printing a conversion from Newton to Celsius.

```
function celsius = newton_to_celsius(newton)
    %NEWTON_TO_CELSIUS   Convert Newton scale to Celsius

    celsius = 100/33 * newton;
end
```
{: .matlab}

```
%TEMPERATURE_CONVERSIONS
% Check temperature conversions between Kelvin, Fahrenheit, Celsius and
% Newton

% Save degree symbol as a variable
deg = char(176);

disp(['Water boils at 100', deg, 'C, which is ', num2str(celsius_to_fahrenheit(100)), deg, 'F.'])
fprintf('Water freezes at %g%sC, which is %g%sF.\n', fahrenheit_to_celsius(32), deg, 32, deg)
fprintf('Absolute zero is 0K, which is %g%sC.\n', kelvin_to_celsius(0), deg)
fprintf('Absolute zero is %g%sF.\n', celsius_to_fahrenheit(kelvin_to_celsius(0)), deg)

fprintf('33 %sN is %g%sC.\n', deg, newton_to_celsius(33), deg)
```
{: .matlab}

~~~
$ git add temperature_conversions.m newton_to_celsius.m
$ git commit			# "Add Newton to Celsius conversion"
~~~
{: .bash}

If we now want to work in our `master` branch. We can switch back by using:

~~~
$ git checkout master 
~~~
{: .bash}
~~~
Switched to branch 'master'
~~~
{: .output}

The script on this branch doesn't have any help text, so let's fix that.

```
%TEMPERATURE_CONVERSIONS
% Convert key temperature values between Fahrenheit, Kelvin, and Celsius

...
```
{: .matlab}

~~~
$ git add temperature_conversions.m
$ git commit			# "Add help text for script"
~~~
{: .bash}

### Merging and resolving conflicts

We are now working on two versions: the main one in our `master` branch and the one
which we may or may not want to keep in our *newton* branch.

At this point let's just quickly visualise the state of our repo,
and we can see the forked commit history reflecting the recent work
on our two branches:

```
git log --graph --all --oneline --decorate
```
{: .bash}

```
* 39ba929 (HEAD -> master) Add help text for script
| * 383bbdd (newton) Add Newton to Celsius conversion
|/  
* d09e099 Print 0K in Fahrenheit
* b7461bf Include Kelvin conversion
* 6fa25fa Add script to print key conversions
* 7e795dc Add functions to convert between C and F
* 414c844 Add help text
* 25f55bf Add Kelvin to Celsius conversion
```
{: .output}

After some thought we decide that we will keep the Newton conversion,
hence it makes sense to now combine the changes from both branches. 
We can do that by *merging* the `newton` branch with the `master` branch. Let's try
doing that:

~~~
$ git checkout master		# Switch branch
$ git merge newton		# Merge newton into master
~~~
{: .bash}
~~~
Auto-merging temperature_conversions.m
CONFLICT (content): Merge conflict in temperature_conversions.m
Automatic merge failed; fix conflicts and then commit the result.
~~~
{: .output}

Git cannot complete the merge because there is a conflict - if you recall,
after creating the new branch, we added different help text for the script on each branch.
We have to resolve the conflict and then complete the merge. We can get
some more detail

~~~
$ git status
~~~
{: .bash}
~~~
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")

Changes to be committed:

	new file:   newton_to_celsius.m

Unmerged paths:
  (use "git add <file>..." to mark resolution)

	both modified:   temperature_conversions.m
~~~
{: .output}

Let's look inside *temperature_conversions.m:

```
%TEMPERATURE_CONVERSIONS
<<<<<<< HEAD
% Convert key temperature values between Fahrenheit, Kelvin, and Celsius
=======
% Check temperature conversions between Kelvin, Fahrenheit, Celsius and
% Newton
>>>>>>> newton
```

The mark-up shows us the parts of the file causing the conflict and the
versions they come from. We now need to manually edit the file to resolve the
conflict. This means removing the mark-up and doing one of:

- Keep the local version, which is the one marked-up by HEAD i.e.
*% Convert key temperature values between Fahrenheit, Kelvin, and Celsius*

- Keep the remote version, which is the one marked-up by paperWJohn
i.e. *% Check temperature conversions between Kelvin, Fahrenheit, Celsius and
% Newton*

- Or manually edit the line to something new which might combine some elements
of the two

Let's edit the file to keep the version from the *newton* branch:

```
%TEMPERATURE_CONVERSIONS
% Check temperature conversions between Kelvin, Fahrenheit, Celsius and
% Newton

% Save degree symbol as a variable
```
{: .matlab}

Then commit our changes:

~~~
$ git add temperature_conversions.m		# Let Git know we have resolved the conflict
$ git commit					# Git has already written a commit message for us:
						# 'Merge branch 'newton'
~~~
{: .bash}

This is where version control proves itself better than DropBox or GoogleDrive,
this ability to merge text files line-by-line and highlight the conflicts
between them, so no work is ever lost.

We can see the two branches merged if we take another look at the log graph:

```
$ git log --graph --decorate --all --oneline
```
{: .bash}

```
*   0377892 (HEAD -> master) Merge branch 'newton'
|\  
| * 383bbdd (newton) Add Newton to Celsius conversion
* | 39ba929 Add help text for script
|/  
* d09e099 Print 0K in Fahrenheit
* b7461bf Include Kelvin conversion
* 6fa25fa Add script to print key conversions
```
{: .output}

### Looking at our history - revisited
We already looked at "going back in time with Git". But now we'll look at it in
more detail to see how moving back relates to branches and we will learn how to
actually undo things.

So far we were moving back in time in one branch
by checking out one of the past commits ---
but we were then in the "detached HEAD" state.

> ## Add a commit to detached HEAD
>
> - Checkout one of the previous commits from our repository.
> - Make some changes and commit them. What happened?
> - Now try to run `git branch`. What can you see?
>
> > ## Solution
> > ```
> > git checkout HEAD~1  			# Check out the commit one before last
> > gedit temperature_conversions.m 	# Make some edits
> > git add temperature_conversions.m 	# Stage the changes
> > git commit 				# Commit the changes
> > git branch 				# You should see a message like the one below,
> > 					# indicating your commit does not belong to a branch
> > ```
> > {: .bash}
> > ```
> > * (HEAD detached from 39ba929)
> >   master
> >   newton
> > ```
> > {: .output}
> > You have just made a commit on a detached HEAD --
> > as you can see from the output above, a new temporary branch has been created,
> > which doesn't have a name.
> >
> > See this [detached HEAD animation] of the above process.
> {: .solution} 
{: .challenge}

[detached HEAD animation]: https://learngitbranching.js.org/?NODEMO&command=git%20checkout%20HEAD~;git%20commit

> ## Abandon the commit on a detached HEAD
> You decide that you want to abandon that commit.
> How would you get back to the current version of your project?
>
> > ## Solution 
> > ```
> > git checkout master
> > ```
> > {: .bash}
> > Git will warn you that you are leaving behind changes that would be lost:
> >
> > The output you see will be slightly different to that below,
> > reflecting your previous commit message and commit ID.
> >
> > ```
> > Warning: you are leaving 1 commit behind, not connected to
> > any of your branches:
> >
> > db7d69f Add empty line for branching exercise
> >
> > If you want to keep them by creating a new branch, this may be a good time
> > to do so with:
> >
> >  git branch new_branch_name db7d69f 
> >
> >  Switched to branch 'master'
> >  Your branch is up-to-date with 'master'.
> > ```
> > {: .output}
> > See this [abandon detached HEAD] animation.
> {: .solution}
{: .challenge}

[abandon detached HEAD]: https://learngitbranching.js.org/?NODEMO&command=git%20checkout%20HEAD~;git%20commit;git%20checkout%20master

> ## Save your changes in a new branch
> Preparation:
>
> - You should be on the `master` branch after that last exercise.
> If not, check out master again: `git checkout master` 
> - Checkout one of the previous commits from your repository.
> - Make some changes, save the file(s), and make a commit on the detached HEAD as
> you did in the first exercise.
> - Run `git branch` to list your local branches, and see that you are on a temporary branch.
>
> This time we want to keep the commit rather than abandon it. 
> - Create a new branch and check it out.
> - Now run `git log` and see that your new commit belongs to this new branch.
> - List your local branches again and see that the temporary branch has gone.
> - Switch back to (i.e. checkout) the `master` branch 
>
> > ## Solution 
> >
> > ```
> > git checkout HEAD~1			# Checkout the commit before last
> > gedit temperature_conversions.m		# Modify one of your files
> > git commit -a				# Commit all the modified files
> > git branch				# List local branches
> > ```
> > {: .bash}
> >
> > ```
> > * (HEAD detached from db7d69f)
> >  master
> >  newton
> > ```
> > {: .output}
> > You are currently on a temporary, unnamed branch, as indicated by the `*`.
> > ```
> > git branch dh-exercise			# Create a new branch
> > git checkout dh-exercise		# Switch to the new branch 
> > ```
> > {: .bash}
> > ```
> > Switched to a new branch 'dh-exericise'
> > ```
> > {: .output}
> > ```
> > git branch				# View local branches
> > ```
> > {: .bash}
> > ```
> > * dh-exericise
> >  master
> >  newton
> > ```
> > {: .output}
> > The commit you made on the detached HEAD now belongs to a named branch
> > (`dh-exercise` in the example above), rather than a temporary branch.
> > ```
> > git checkout master			# Switch back to the 'master' branch
> > ```
> > {: .bash}
> > See this [new branch animation] for the key points in this exercise.
> {: .solution}
{: .challenge}

[new branch animation]: https://learngitbranching.js.org/?NODEMO&command=git%20checkout%20HEAD~;git%20commit;git%20branch%20dh-ex;git%20checkout%20dh-ex;git%20checkout%20master
