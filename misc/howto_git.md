# Introduction to GIT

## What is it?

Git is a version control system for tracking and
coordinating changes in repositories. It is primarily used
for tracking changes in code, used highly in software
development, but it is however applicable to track changes
of any files in any environment.

## Why use it?

Instead of having to constantly download/upload entire
files that have changes, git pushes only the changed lines
and allows for the users to track any changes allowing for
the users to revert to a previous version at will.

### Good references for better guides

I have used some of these guides myself to learn, and am
still learning - please read them for detailed information.

* [http://rogerdudler.github.io/git-guide/](http://rogerdudler.github.io/git-guide/)
* [https://git-scm.com/book/en/v2/Getting-Started-Git-Basics](https://git-scm.com/book/en/v2/Getting-Started-Git-Basics)
* [http://learngitbranching.js.org/](http://learngitbranching.js.org/)

## Basic Usage:

### Clone

The ``clone`` command is used to download the repository on
to your computer.

Usage:

```
git clone <repository_here>
```

Example:

```
git clone https://github.com/NatoliChris/documentation.git
```

### Pull

This command fetches any changes from the repository that
anyone has pushed up. *Note:* this will always pull changes
down, including any conflicting changes that will need to
be handled.

Usage:

```
git pull [branchname]
```

Note: the branch name is optional, if it is left out then
it will fetch all.


### Commit.

This is the command to stage the changes ready to push up.
It will also let you write a comment detailing what changes
were made in this particular commit.

Usages:

```
git commit
```

Commit all files (will open ``$EDITOR``)
```
git commit -a
```

Commit with a message:

```
git commit -m "message here"
```

*Note:* There are a couple more that you can do, please
check that out for more details, this is just an intro ;)

### Push

This is the command to push your commits to the repository, 
it uploads all the commits and changes that you've prepared.
There are many options - please read the documentations for more info.

Basic Usage:

```
git push [branchname]
```
