---
layout: page
title: Version Control with Git
subtitle: Exploring History
minutes: 25
---
> ## Learning Objectives {.objectives}
>
> *   Identify and use Git commit numbers.
> *   Compare various versions of tracked files.
> *   Restore old versions of files.

If we want to see what we changed at different steps, we can use `git diff`
again, but with the notation `HEAD~1`, `HEAD~2`, and so on, to refer to old
commits:

~~~ {.bash}
$ git diff HEAD~1 _config.yml
~~~
~~~ {.output}
diff --git a/_config.yml b/_config.yml
index 3c18501..36bbe2a 100644
--- a/_config.yml
+++ b/_config.yml
@@ -6,7 +6,7 @@
 name: Bruno Grande

 # Short bio or description (displayed in the header)
-description: Web Developer from Somewhere
+description: Computational biologist in cancer genomics

 # URL of your avatar or profile pic (you could use your GitHub profile pic)
 avatar: https://raw.githubusercontent.com/barryclark/jekyll-now/master/images/jekyll-logo.png
~~~
~~~ {.bash}
$ git diff HEAD~2 _config.yml
~~~
~~~ {.output}
diff --git a/_config.yml b/_config.yml
index 494da8c..36bbe2a 100644
--- a/_config.yml
+++ b/_config.yml
@@ -3,10 +3,10 @@
 #

 # Name of your site (displayed in the header)
-name: Your Name
+name: Bruno Grande

 # Short bio or description (displayed in the header)
-description: Web Developer from Somewhere
+description: Computational biologist in cancer genomics

 # URL of your avatar or profile pic (you could use your GitHub profile pic)
 avatar: https://raw.githubusercontent.com/barryclark/jekyll-now/master/images/jekyll-logo.png
~~~

In this way, we can build up a chain of commits.
The most recent end of the chain is referred to as `HEAD`; we can refer to 
previous commits using the `~` notation, so `HEAD~1` (pronounced "head minus 
one") means "the previous commit", while `HEAD~123` goes back 123 commits from 
where we are now.

We can also refer to commits using those long strings of digits and letters 
that `git log` displays.
These are unique IDs for the changes, and "unique" really does mean unique:
every change to any set of files on any computer has a unique 40-character 
identifier.
Our first commit was given the ID f570573f86fd615208d5900358fa06dd563a9402, so 
let's try this:

~~~ {.bash}
$ git diff f570573f86fd615208d5900358fa06dd563a9402 _config.yml
~~~
~~~ {.output}
diff --git a/_config.yml b/_config.yml
index 494da8c..36bbe2a 100644
--- a/_config.yml
+++ b/_config.yml
@@ -3,10 +3,10 @@
 #

 # Name of your site (displayed in the header)
-name: Your Name
+name: Bruno Grande

 # Short bio or description (displayed in the header)
-description: Web Developer from Somewhere
+description: Computational biologist in cancer genomics

 # URL of your avatar or profile pic (you could use your GitHub profile pic)
 avatar: https://raw.githubusercontent.com/barryclark/jekyll-now/master/images/jekyll-logo.png
~~~

That's the right answer, but typing out random 40-character strings is 
annoying, so Git lets us use just the first few characters:

~~~ {.bash}
$ git diff f57057 _config.yml
~~~
~~~ {.output}
diff --git a/_config.yml b/_config.yml
index 494da8c..36bbe2a 100644
--- a/_config.yml
+++ b/_config.yml
@@ -3,10 +3,10 @@
 #

 # Name of your site (displayed in the header)
-name: Your Name
+name: Bruno Grande

 # Short bio or description (displayed in the header)
-description: Web Developer from Somewhere
+description: Computational biologist in cancer genomics

 # URL of your avatar or profile pic (you could use your GitHub profile pic)
 avatar: https://raw.githubusercontent.com/barryclark/jekyll-now/master/images/jekyll-logo.png
~~~


All right! So we can save changes to files and see what we've changed&mdash;
now how can we restore older versions of things?
Let's suppose we accidentally overwrite our file:

~~~ {.bash}
$ nano _config.yml
$ head _config.yml
~~~
~~~ {.output}
#
# This file contains configuration flags to customize your site
#

# Name of your site (displayed in the header)
name: Bruno Grande

# Short bio or description (displayed in the header)
description: Jobless, hopeless, helpless...
~~~

`git status` now tells us that the file has been changed,
but those changes haven't been staged:

~~~ {.bash}
$ git status
~~~
~~~ {.output}
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

	modified:   _config.yml

no changes added to commit (use "git add" and/or "git commit -a")
~~~

We can put things back the way they were by using `git checkout`:

~~~ {.bash}
$ git checkout HEAD _config.yml
$ cat _config.yml
~~~
~~~ {.output}
#
# This file contains configuration flags to customize your site
#

# Name of your site (displayed in the header)
name: Bruno Grande

# Short bio or description (displayed in the header)
description: Computational biologist in cancer genomics
~~~

As you might guess from its name, `git checkout` checks out (i.e., restores) an 
old version of a file.
In this case, we're telling Git that we want to recover the version of the file 
recorded in `HEAD`, which is the last saved commit.
If we want to go back even further, we can use a commit identifier instead:

~~~ {.bash}
$ git checkout f57057 _config.yml
~~~

It's important to remember that we must use the commit number that identifies 
the state of the repository *before* the change we're trying to undo.
A common mistake is to use the number of the commit in which we made the change 
we're trying to get rid of.
In the example below, we want to retrieve the state from before the most
recent commit (`HEAD~1`), which is commit `f57057`:

![Git Checkout](fig/git-checkout.svg)

So, to put it all together:

> ## How Git works, in cartoon form {.callout}
> ![http://figshare.com/articles/How_Git_works_a_cartoon/1328266](fig/git_staging.svg)

> ## Simplifying the Common Case {.callout}
>
> If you read the output of `git status` carefully,
> you'll see that it includes this hint:
>
> ~~~ {.bash}
> (use "git checkout -- <file>..." to discard changes in working directory)
> ~~~
>
> As it says,
> `git checkout` without a version identifier restores files to the state saved 
in `HEAD`.
> The double dash `--` is needed to separate the names of the files being 
> recovered from the command itself: without it, Git would try to use the name 
> of the file as the commit identifier.

The fact that files can be reverted one by one tends to change the way people 
organize their work.
If everything is in one large document, it's hard (but not impossible) to undo 
changes to the introduction without also undoing changes made later to the 
conclusion.
If the introduction and conclusion are stored in separate files, on the other 
hand, moving backward and forward in time becomes much easier.


> ## Recovering Older Versions of a File {.challenge}
>
> Jennifer has made changes to the Python script that she has been working on for weeks, and the
> modifications she made this morning "broke" the script and it no longer runs. She has spent
> ~ 1hr trying to fix it, with no luck...
>
> Luckily, she has been keeping track of her project's versions using Git! Which commands below will
> let her recover the last committed version of her Python script called
> `data_cruncher.py`?
>
> 1. 
>
>     ~~~
>     $ git checkout HEAD
>     ~~~
> 2. 
>
>     ~~~
>     $ git checkout HEAD data_cruncher.py
>     ~~~
> 3. 
>
>     ~~~
>     $ git checkout HEAD~1 data_cruncher.py
>     ~~~
> 4. 
>
>     ~~~
>     $ git checkout <unique ID of last commit> data_cruncher.py
>     ~~~
> 5. Both 2 & 4


> ## Understanding Workflow and History {.challenge}
>
> What is the output of cat venus.txt at the end of this set of commands?
>
> ~~~ {.bash}
> $ cd planets
> $ nano venus.txt #input the following text: Venus is beautiful and full of love
> $ git add venus.txt
> $ nano venus.txt #add the following text: Venus is too hot to be suitable as a base
> $ git commit -m "comments on Venus as an unsuitable base"
> $ git checkout HEAD venus.txt
> $ cat venus.txt #this will print the contents of venus.txt to the screen
> ~~~
>
> 1. 
> 
>     ~~~ {.output}
>     Venus is too hot to be suitable as a base
>     ~~~
>
> 2. 
> 
>     ~~~ {.output}
>     Venus is beautiful and full of love
>     ~~~
>
> 3. 
> 
>     ~~~ {.output}
>     Venus is beautiful and full of love
>     Venus is too hot to be suitable as a base
>     ~~~
>
> 4. 
> 
>     ~~~ {.output}
>     Error because you have changed venus.txt without committing the changes
>     ~~~
