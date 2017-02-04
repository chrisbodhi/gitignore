### Getting a handle on tracked and untracked files/dirs in git
Let's say we have just initialized an empty git repository locally located at: `~/lab/pirate` by running `git init` from the command line.

We are going to create several files in our project: 

![gitT1](images/t1.png)

Our directory now looks like this:  
 
![gitT2](images/t2.png)

_Note: our directory would also include a `.git` directory that is created when a repository is initialized, but its contents are beyond the scope of this readme, so we will ignore it._

Now let's look at the status of the git repository. We can see nearly all of our files showing as untracked files in our working tree. We added the `--ignored` argument to our status command, so we are also seeing that `man-o-war.class` is being ignored by git. The reason for this is that my locally configured global gitignore is set to ignore `.class` files. If we have a global gitignore set, across our entire machine, any local git repository will first be filtered through our global `gitconfig` settings--the global gitignore is set in the `gitconfig` which usually resides wherever your git application is installed. We'll develop on the global gitignore vs repository specific gitignores further as we go, but for now just know that the global gitignore is the reason that `.class` files are being ignored from this git repository.

![gitT3](images/t3.png)
![gitS1](images/s1.png)
![gitS2](images/s2.png)

Now let's suppose we want to commit these new files to our repository, so we add them to our index with:

```
git add --all
```

Now when we check our status we see that all of our untracked files are now being listed as new files on the index.  
![Warning](http://www.nzsee.org.nz/images/exclam_icon.png)  
**This is the important point** where all our files went from existing on our file system and in our git working tree (but untracked by the git index), to existing in our file system, our git working tree, AND our git index (they will now be tracked by git and git will check for future modifications to them). 

**Note that our ignored file is not part of any of this process--it exists in our file system, but is not considered part of the repository by git.**

![gitT4](images/t4.png)
![gitS3](images/s3.png)
![gitS4](images/s4.png)

We will now commit our newly added files from the index with:

```
git commit
```
![gitS5](images/s5.png)

Now that our files have been commited, a git status check shows a clean working tree and the `man-o-war.class` remains ignored:

![gitT5](images/t5.png)


If we check our index, we will see all the files we are tracking:

![gitT6](images/t6.png)
![gitS6](images/s6.png)

Now let's add a secret key password inside a yaml configuration file:

```
touch booty/key.yaml
```

This is a new file into the working tree, so when we check our status, we see it as untracked:

![gitT7](images/t7.png)
![gitS7](images/s7.png)

Since this secret key is what we use to get inside of our `booty/treasure.json`, we'd prefer to keep it out of source control. This secret key is very specific to our pirate project and so not appropriate for our global gitignore. As such we're going to add a top level `.gitignore` to the root of our repository so it only affects this repository. Gitignores are cumulative, so global gitignores combine with directory gitignores to produce a single set of ignores that only depend on the tree paths they reside in (you can have multiple gitignores throughout your project however one at the root is nearly always sufficient). 

We create a `.gitignore` file and add the `key.yaml` file to it.

We also have decided that we have our own personal moral compass and aren't going to be abiding by the pirate code any more, so let's also put the `code.md` into the repository's `.gitignore`.

Now we see the contents of our `.gitignore` and our file system tree:

![gitT8](images/t8.png)
![gitS8](images/s8.png)

Big suprise however when we check our git status and see that while the `key.yaml` is now being successfully ignored, our `code.md` is not!

![gitT9](images/t9.png)
![gitS9](images/s9.png)

The reason for this is that we had previously added `code.md` to our index and committed it. Because of this, git started tracking the file:

![gitT10](images/t10.png)
![gitS10](images/s10.png)

![Warning](http://www.nzsee.org.nz/images/exclam_icon.png)  
**This is a very critical point.** Ignored files/dirs are only ignored if they are not being tracked. If they are being tracked, git assumes that they are being tracked for good reason and won't stomp on your index. If we want to ensure that `code.md`:

1. stops being tracked by git
2. is ignored by any attempts to track it or add it to the index in the future

We must have the following in place:

1. ensure that `code.md` is not part of our index
2. ensure that `code.md` is set to be ignored

In our case we have taken care of #2 by adding the file to our `.gitignore`, but we must tell git to stop tracking it. We can do that like so:

**From the CLI:**

```
git rm --cached <FILE1> <FILE2> <FILE3>  
git rm --cached -r <DIRECTORY1> <DIRECTORY2>  
```

OR

**From SourceTree:**
 
Right click on a file and select “Stop Tracking”

---

We'll go ahead and untrack `code.md` with `git rm --cached code.md`. Now when we look at our status, we can see that git has removed `code.md` from the index--meaning it will no longer be tracked--and because it is already in our `.gitignore`, it is now marked as an ignored file and will not be added to our index even when we `git add --all`.

![gitT11](images/t11.png)
![gitS11](images/s11.png)
![gitS12](images/s12.png)

Keep in mind the separation of git entities throughout this process:

* **git directory** -- location on your file system where the `.git` directory resides; responsible for populating the files into the git directory based on what commit you currently have checked out  

* **git working tree** -- the file tree from a specific commit that you currently have checked out

* **git index** (also called staging) -- a file that lists information about what will go into your next commit

So even though we have deleted `code.md` from our index, it still resides in our working tree and our git directory (the same as `key.yaml` and `man-o-war.class`):

![gitT12](images/t12.png)
![gitS13](images/s13.png)

With this understanding, we can manage files that we need for our work which ought to remain local to our machine and outside of version control.

e.g.

* Eclipse project settings and personal preferences
* classpaths
* bytecode and other binaries
* local environment settings/configuration

---

### Using an Adjacent global gitignore:
As many of these files are common to all of us, there are a number of files we can take care of ignoring with an organization-wide gitignore. That gitignore resides here in this repository. The repository will be updated and can be pulled from by any member of Adjacent's development team. 

1. To start using this global gitignore, begin by cloning this repo: <https://bitbucket.org/adjacentdev/gitignore_global>
1. Add the global gitignore file to your gitconfig:

**From the CLI:**

```
git config --global core.excludesfile [PATH TO WHERE YOU CLONED REPO]/.gitignore_global
```
 
OR
 
**From SourceTree:**

Add the `.gitignore_global` from the path of the above cloned repo to the “Global Ignore List”

#### Tools > Options > Git tab. 

While you're here, take this opportunity to make sure your git push is set to `simple` ([why?](http://stackoverflow.com/a/21865319)) and to update your embedded git.

![gitS14](images/s14.png)

---

Once that's done, all your git repositories on your local machine will start using this global gitignore. Use the methods above to manage repositories and help keep them clean and correct.  
