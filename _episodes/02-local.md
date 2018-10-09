---
title: "Tracking changes with a local repository"
teaching: 35
exercises: 0
questions:
- "How do I get started with Git?"
- "Where does Git store information?"
objectives:
- "Know how to set up a new Git repository."
- "Understand how to start tracking files."
- "Be able to commit changes to your repository."
keypoints:
- "`git init` initializes a new repository"
- "`git status` shows the status of a repository"
- "Files can be stored in a project’s `working directory` (which users see), the `staging area` (where the next commit is being built up) and the `local repository` (where commits are permanently recorded)"
- "`git add` puts files in the staging area"
- "`git commit` saves the staged content as a new commit in the local repository"
- "Always write a log message when committing changes"
---

Version control is centred round the notion of a *repository* which holds your
directories and files. We'll start by looking at a local repository. The local
repository is set up in a directory in your local filesystem (local machine).
For this we will use the command line interface.

> ## Why use the command line?
> There are lots of graphical user interfaces (GUIs) for using Git: both stand-alone
> and integrated into IDEs (e.g. MATLAB, Rstudio).
> We are deliberately not using a GUI for this course because: 
>
> * you will have a better understanding of how the git comands work
> (some functionality is often missing and/or unclear in GUIs)
> * you will be able to use Git on any computer
> (e.g. remotely accessing HPC systems, which generally only have Linux command line access)
> * you will be able to use any GUI, rather than just the one you have learned
{: .callout}

## Setting up Git
Git is already installed on the training machines, whether you're using Windows or Linux.
Instructions for setting up Git on your own machine are given under [setup]({{ page.root }}/setup).

## Tell Git who we are

As part of the information about changes made to files Git records who made
those changes. In teamwork this information is often crucial (do you want to
know who rewrote your 'Conclusions' section?). So, we need to tell Git about
who we are (note that you need to enclose your name in quote marks):

~~~
$ git config --global user.name "Your Name" 			# Put your quote marks around your name
$ git config --global user.email yourname@yourplace.org
~~~
{: .language-bash}

## Set a default editor

When working with Git we will often need to provide some short but useful
information. In order to enter this information we need an editor. We'll now
tell Git which editor we want to be the default one (i.e. Git will always bring
it up whenever it wants us to provide some information).

You can choose any editor available on your system. For the purpose of this
session we'll use *notepad*:

~~~
$ git config --global core.editor notepad				# Windows users only.
								# Linux users should use gedit: see below.
~~~
{: .language-bash}

To set up alternative editors, follow the same notation e.g.
`git config --global core.editor gedit`, `git config --global core.editor vi`,
`git config --global core.editor xemacs`.

## Git's global configuration

We can now preview (and edit, if necessary) Git's global configuration (such as
our name and the default editor which we just set up). If we look in our home
directory, we'll see a `.gitconfig` file,

~~~
$ cat ~/.gitconfig 
    [user] name = Your Name email = yourname@yourplace.org
    [core] editor = notepad
~~~
{: .language-bash}

**These global configuration settings will apply to any new Git repository
you create on your computer.**
i.e. the `--global` commands above are only required once per computer.

---

## Create a new repository with Git

We will be working with a simple example in this tutorial. It will be a series
of MATLAB functions to convert units, which we will then use to develop
a series of figures and look-up tables.

 First, let's create a directory within your home directory:

```
$ cd								# Switch to your home directory.
$ pwd								# Print working directory (output should be /home/<username>)
$ mkdir conversions 
$ cd conversions
```	
{: .language-bash}

Now, we need to set up this directory up to be a Git repository (or "initiate
the repository"):

~~~
$ git init 
~~~
{: .language-bash}
~~~
Initialized empty Git repository in /home/user/conversions/.git/
~~~
{: .output}

The directory *conversions* is now our working directory. 

 If we look in this directory, we'll find a `.git` directory:

~~~
$ ls .git
~~~
{: .language-bash}
~~~
branches  config  description  HEAD  hooks  info  objects refs
~~~
{: .output}
The `.git` directory contains Git's configuration files. Be careful not to
accidentally delete this directory!

## Tracking files with a git repository

Now, we'll create a file for our first conversion.
The file will be called `kelvin_to_celsius.m` and should be saved in the
working directory.
We can either create a new file from the Git prompt

```
notepad kelvin_to_celsius.m
```
{: .language-bash}

or we can create the file from within MATLAB.
Just be sure to save it to the working directory --- recall that we can
return the current directory using the following command:

```
pwd
```
{: .language-bash}

Create the file with the following contents:

```
function celsius = kelvin_to_celsius(kelvin)
    celsius = kelvin - 273.15
end
```
{: .language-matlab}


`git status` allows us to find out about the current status
of files in the repository. So we can run,

~~~
$ git status
~~~
{: .language-bash}
~~~
On branch master

Initial commit

Untracked files:
(use "git add <file>..." to include in what will be committed)

kelvin_to_celsius.m

nothing added to commit but untracked files present (use "git add" to track)
~~~
{: .output}

Information about what Git knows about the directory is displayed. We are on
the `master` branch, which is the default branch in a Git respository
(one way to think of branches is like parallel versions of the project --- more
on branches later).

For now, the important bit of information is that our file is listed as
**Untracked** which means it is in our working directory but Git is not
tracking it --- that is, any changes made to this file will not be recorded by
Git.

## Add files to a Git repository
To tell Git about the file, we will use the `git add` command:

~~~
$ git add kelvin_to_celsius.m 
$ git status 
~~~
{: .language-bash}
~~~
On branch master

Initial commit

Changes to be committed:
(use "git rm --cached <file>..." to unstage)

      	new file:   kelvin_to_celsius.m
~~~
{: .output}

Now our file is listed underneath where it says **Changes to be committed**. 
    
`git add` is used for two purposes. Firstly, to tell Git that a given file
should be tracked. Secondly, to put the file into the Git **staging area**
which is also known as the *index* or the *cache*. 

The staging area can be viewed as a "loading dock", a place to hold files we have
added, or changed, until we are ready to tell Git to record those changes in the
repository. 

![The staging area](../fig/git-staging-area.svg)

## Commit changes

In order to tell Git to record our change, our new file, into the repository,
we need to  **commit** it:

~~~
$ git commit
	# Now type a commit message: 'Add kelvin to celsius conversion'
	# Save the commit message and close your text editor (notepad etc.)
~~~
{: .language-bash}

Our default editor will now pop up. Why? Well, Git can automatically figure out
that directories and files are committed, and by whom (thanks to the information
we provided before) and even, what changes were made, but it cannot figure out
why. So we need to provide this in a commit message.

If we save our commit message **and exit the editor**, Git will now commit our file.

~~~
[master (root-commit) 21cfbde] 
1 file changed, 3 insertions(+) Add kelvin to celsius conversion
create mode 100644 kelvin_to_celsius.m
~~~
{: .output}

This output shows the number of files changed and the number of lines inserted
or deleted across all those files. Here, we have changed (by adding) 1 file and
inserted 3 lines.

Now, if we look at the status, our staging area is empty because we've
commited those previously staged changes:

~~~
$ git status
~~~
{: .language-bash}
~~~
On branch master
nothing to commit, working directory clean
~~~
{: .output}

The output from the `git status` command means that we have a clean directory
i.e. no tracked but modified files. 

Now we will work a bit further on our *kelvin_to_celsius.m* file by adding an H1 line.
Edit your file as below (then save it):
   
```
function celsius = kelvin_to_celsius(kelvin)
    %KELVIN_TO_CELSIUS   Convert Kelvin to degrees Celsius

    celsius = kelvin - 273.15
end
```
{: .language-matlab}

If we now run,

~~~
$ git status
~~~
{: .language-bash}

we see a *changes not staged for commit* section and our file is marked as
modified: 

~~~
On branch master
Changes not staged for commit:
(use "git add <file>..." to update what will be committed)
(use "git checkout -- <file>..." to discard changes in working directory)

     modified:   kelvin_to_celsius.m

no changes added to commit (use "git add" and/or "git commit -a")
~~~
{: .output}

This means that a file Git knows about has been modified by us but
has not yet been committed. So we can add it to the staging area and then
commit the changes:
     
~~~
$ git add kelvin_to_celsius.m
$ git commit							# "Add help text"
~~~
{: .language-bash}

Note that in this case we used `git add` to put *kelvin_to_celsius.m* in the staging
area. Git already knows this file should be tracked but doesn't know if we want
to commit the changes we made to the file
and hence we have to add the file to the staging area. 

It can sometimes be quicker to provide our commit messages at the command-line
by doing `git commit -m "Add help text"`.

So far we have dealt with one file at a time,
but sometimes a logical set of changes will span multiple files.

Next we'll write a couple of functions to convert
Fahrenheit to Celsius and vice-versa.

Create the following files: `fahrenheit_to_celsius.m`

```
function celsius = fahrenheit_to_celsius(fahrenheit)
    %FAHRENHEIT_TO_CELSIUS   Convert Fahrenheit to Celsius

    celsius = (fahrenheit-32) * (5/9);
end
```
{: .language-matlab}

and `celsius_to_fahrenheit.m`

```
function fahrenheit = celsius_to_fahrenheit(celsius)
    %CELSIUS_TO_FAHRENHEIT   Convert Celsius to Fahrenheit 

    fahrenheit = celsius*(9/5) + 32;
end
```
{: .language-matlab}

Now add both the files to the staging area,
and make a commit:

```
$ git add celsius_to_fahrenheit.m fahrenheit_to_celsius.m
$ git commit -m 'Add functions to convert between C and F
```
{: .language-bash}

![The Git commit workflow](../fig/git-committing.svg)

As something of a sanity check, we might just want to test the output of these
functions --- we'll do that with the following script:

```
%TEMPERATURE_CONVERSIONS

% Save degree symbol as a variable
deg = char(176);

disp(['Water boils at 100', deg, 'C, which is ', num2str(celsius_to_fahrenheit(100)), deg, 'F.'])
fprintf('Water freezes at %g%sC, which is %g%sF.\n', fahrenheit_to_celsius(32), deg, 32, deg)
```
{: .language-matlab}

Then we'll commit the script:

```
$ git add temperature_conversions.m
$ git commit
	# Commit message: 'Add script to print key conversions'
```
{: .language-bash}
