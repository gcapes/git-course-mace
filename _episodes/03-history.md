---
title: "Looking at history and differences"
teaching: 30
exercises: 5
questions:
- "How do I get started with Git?"
- "Where does Git store information?"
objectives:
- "Be able to view history of changes to a repository"
- "Be able to view differences between commits"
- "Understand how and when to use tags to label commits"
keypoints:
- "`git log` shows the commit history"
- "`git diff` displays differences between commits"
- "`git checkout` recovers old versions of files"
- "`HEAD` points to the commit you have checked out"
- "`master` points to the tip of the `master` branch"
---


### Looking at differences

We've printed a couple of key values using our script,
but what about the Kelvin scale?
We've not even mentioned it!
Add a line to display 0K in degrees C and save the script,
but do not commit it yet.

~~~
fprintf('Absolute zero is 0K, which is %g%sC.\n', kelvin_to_celsius(0), deg)
~~~
{: .matlab}

We can review the changes that we made using:

~~~
$ git diff temperature_conversions.m		# View changes to file
~~~
{: .bash}

This shows the difference between the latest copy in the repository and the
unstaged changes we have made.

* `-` means a line was deleted.  
* `+` means a line was added.  
* Note that a line that has been edited is shown as a removal of the old line and an
addition of the updated line.

Looking at differences between commits is one of the most common activities.
The `git diff` command itself has a number of [useful
options](http://git-scm.com/docs/git-diff.html).

There is also a range of GUI-based tools for looking at differences and
editing files. For example:

* [Diffmerge](https://sourcegear.com/diffmerge/) (Free, cross-platform)
* [WinMerge](http://winmerge.org/) - open source tool available for Windows;
* GitHub [Compare
view](https://help.github.com/articles/comparing-commits-across-time)

Git can be configured to use graphical diff tools, and this is functionality
is accessed using `git difftool` in place of `git diff`.

Now commit the change to our script `temperature_conversions.m`:
```
$ git add temperature_conversions.m
$ git commit			# "Include Kelvin conversion"
```
{: .bash}

### Looking at our history

To see the history of changes that we made to our repository (the most recent
changes will be displayed at the top):

~~~
$ git log
~~~
{: .bash}

```
commit b7461bff372728da9829b3f3dd4fb1e70b9fc4bb
Author: Your Name <your.name@manchester.ac.uk>
Date:   Mon Jul 30 09:15:37 2018 +0100

    Include Kelvin conversion

commit 6fa25fa6a6ff9ecdc8be1042f552192ca7befc21
Date:   Mon Jul 30 09:14:30 2018 +0100

    Add script to print key conversions

commit 7e795dca7105a78a8b4d446b653996518e1a06d7
Author: Your Name <your.name@manchester.ac.uk>
Date:   Mon Jul 30 09:12:41 2018 +0100

    Add functions to convert between C and F

commit 414c8445efeecade38e2abf6181aa97e21a785ec
Author: Your Name <your.name@manchester.ac.uk>
Date:   Mon Jul 30 09:11:34 2018 +0100

    Add help text

commit 25f55bf57e767edb54303fac10b88cc6f69c945a
Author: Your Name <your.name@manchester.ac.uk>
Date:   Mon Jul 30 09:10:15 2018 +0100

    Add Kelvin to Celsius conversion

```
{: .output}

The output shows (on separate lines):
- the commit identifier (also called revision number) which
uniquely identifies the changes made in this commit
- author
- date
- your commit message

Git automatically assigns an identifier (e.g. 4dd7f5) to each commit
made to the repository
--- we refer to this as *COMMITID* in the code blocks below.
In order to see the changes made between any earlier commit and our
current version, we can use  `git diff` followed by the commit identifier of the
earlier commit:

~~~
$ git diff COMMITID		# View differences between current version and COMMITID
~~~
{: .bash}

And, to see changes between two commits:

~~~
$ git diff OLDER_COMMITID NEWER_COMMITID
~~~
{: .bash}

Using our commit identifiers we can set our working directory to contain the
state of the repository as it was at any commit. So, let's go back to the very
first commit we made,

~~~
$ git log 
$ git checkout INITIAL_COMMITID
~~~
{: .bash}

We will get something like this:

~~~
Note: checking out '25f55bf5'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b new_branch_name
  
HEAD is now at 25f55bf... Add Kelvin to Celsius conversion
~~~
{: .output}

This strange concept of the 'detached HEAD' is covered in the next section ...
just bear with me for now!

If we look at `kelvin_to_celsius.m` we'll see it's our very first version. And if we
look at our directory,

~~~
$ ls
~~~
{: .bash}
~~~
kelvin_to_celsius.m
~~~
{: .output}

then we see that our other files are missing. But, rest easy, while they're
gone from our working directory, they're still in our repository. We can jump back
to the latest commit by doing:

~~~
$ git checkout master
~~~
{: .bash}

And all our files will be there once more,

~~~
$ ls
~~~
{: .bash}
~~~
celsius_to_fahrenheit.m  fahrenheit_to_celsius.m  kelvin_to_celsius.m  temperature_conversions.m
~~~
{: .output}
So we can get any version of our files from any point in time. In other words,
we can set up our working directory back to any stage it was when we made
a commit.

### The `HEAD` and `master` pointers

*HEAD* is a reference, or pointer, which points to the branch at the commit where
you currently are. 
We said previously that `master` is the default branch. But `master` is
actually a pointer - that points to the tip of the `master` branch (the sequence
of commits that is created by default by Git). You may think of `master` as two
things:

1. a pointer
2. the default branch.

Before we checked out one of the past commits, the *HEAD* pointer was pointing to
`master` i.e. the most recent commit of the `master` branch. 
After checking out one of the past commits, *HEAD* was pointing to that commit i.e.
not pointing to master any more. 
That is what Git means by a 'detached HEAD' state and advises us that if we want to make a commit
now, we should create a new branch to retain these commits. 

![Checking out a previous commit - detached head](../fig/detached-head.svg)

If we created a new commit without first creating a new branch, i.e. working from
the 'detached HEAD' these commits would not overwrite any of our existing work,
but they would not belong to any branch. In order to save this work, we would need
to checkout a new branch. To discard any changes we make from the detached HEAD
state, we can just checkout master again.

## Visualising your own repository as a graph
If we use `git log` with a couple of options, we can display the history as a graph,
and decorate those commits corresponding to Git references (e.g. `HEAD`, `master`):

~~~
$ git log --graph --decorate --oneline
~~~
{: .bash}

```
* b7461bf (HEAD -> master) Include Kelvin conversion
* 6fa25fa Add script to print key conversions
* 7e795dc Add functions to convert between C and F
* 414c844 Add help text
* 25f55bf Add Kelvin to Celsius conversion
```
{: .output}

Notice how `HEAD` and `master` point to the same commit.
Now checkout a previous commit again, and look at the graph again.
We can display, this time specifying that we want to look at `--all` the history,
rather than just up to the current commit.

```
$ git checkout HEAD~				# This syntax refers to the commit before HEAD
$ git log --graph --decorate --oneline --all
```
{: .bash}

```
* b7461bf (master) Include Kelvin conversion
* 6fa25fa (HEAD) Add script to print key conversions
* 7e795dc Add functions to convert between C and F
* 414c844 Add help text
* 25f55bf Add Kelvin to Celsius conversion
```
{: .output}

Notice how `HEAD` no longer points to the same commit as `master`.
Let's return to the current version of the project by checking out `master` again.

```
$ git checkout master
```
{: .bash}

### Using tags as nicknames for commit identifiers

Commit identifiers are long and cryptic. Git allows us to create tags, which
act as easy-to-remember nicknames for commit identifiers.

For example,

```    
$ git tag INITIAL_SCRIPT
```
{: .bash}

We can list tags by doing:

```    
$ git tag
```
{: .bash}

Now add a line to the script to display 0K in Fahrenheit, then commit the change:

```
fprintf('Absolute zero is %g%sF.\n', celsius_to_fahrenheit(kelvin_to_celsius(0)), deg) 
```
{: .matlab}

```
$ git add temperature_conversions.m
$ git commit -m "Print 0K in Fahrenhit" temperature_conversions.m
```
{: .bash}

We can checkout our previous version using our tag instead of a commit
identifier.

```    
$ git checkout INITIAL_SCRIPT
```
{: .bash}

And return to the latest checkout,

```    
$ git checkout master
```
{: .bash}

> ## Top tip: tag significant events 
> When do you tag? Well, whenever you might want to get back to the exact
> version you've been working on. For a paper, this might be a version that has
> been submitted to an internal review, or has been submitted to a conference.
> For code this might be when it's been submitted to review, or has been
> released.
{: .callout}

> ## Where to create a Git repository?
> Avoid creating a Git repository within another Git repository.
> Nesting repositories in this way causes the 'outer' repository to
> track the contents of the 'inner' repository - things will get confusing!
{: .callout}

> ## Exercise: "bio" Repository 
>
> - Create a new Git repository on your computer called "bio"
> - Be sure not to create your new repo within the 'conversions' repo (see above)
> - Write a three-line biography for yourself in a file called **me.txt**
> - Commit your changes
> - Modify one line, add a fourth line, then save the file
> - Display the differences between the updated file and the original
>
> You may wish to use the faded example below as a guide
>
> ```
> cd ..                # Navigate out of the conversions directory
>                      # Avoid creating a repo within a repo - confusion will arise!
> mkdir ___            # Create a new directory called 'bio'
> cd ___               # Navigate into the new directory
> git ____             # Initialise a new repository
> _____ me.txt         # Create a file and write your biography
> git ___ me.txt       # Add your biography file to the staging area
> git ______           # Commit your staged changes
> _____ me.txt         # Edit your file
> git ____ me.txt      # Display differences between your modified file and the last committed version
> ```
> {: .bash}
>
> > ## Solution
> >
> > ```
> > cd ..                # Navigate out of the conversions directory
> >                      # Avoid creating a repo within a repo - confusion will arise!
> > mkdir bio            # Create a new directory
> > cd bio               # Navigate into the new directory
> > git init             # Initialise a new repository
> > gedit me.txt         # Create a file and write your biography
> > git add me.txt       # Add your biography file to the staging area
> > git commit           # Commit your staged changes
> > gedit me.txt         # Edit your file
> > git diff me.txt      # Display differences between your modified file and the last committed version
> > ```
> > {: .bash}
> {: .solution}
{: .challenge}

