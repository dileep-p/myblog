---
author: vishnur66
comments: false
date: 2013-10-20 14:52:38+00:00
layout: post
link: http://www.vishnu-tech.com/2013/10/tracking-branches-and-remote-tracking-branches/
published: false
slug: tracking-branches-and-remote-tracking-branches
title: “Tracking Branches” And “Remote-Tracking Branches”
wordpress_id: 265
tags:
- git
- remote branches
- track branches
- track remote branches
---

## Commands used in this section:





	
  * git add

	
  * git commit

	
  * git clone

	
  * git push

	
  * git fetch

	
  * git merge

	
  * git pull




## Let’s Talk Tracking


When using remote repositories, you’ll see the terms:



	
  * Tracking Branches

	
  * Remote Tracking Branches


Both Tracking Branches and Remote Tracking Branches are created on the git client when a **git clone** command is used.


### Tracking Branches and Remote Tracking Branches: Summary


Below is a high-level summary of tracking branches and remote tracking branches. The rest of this page gives examples of tracking and remote tracking branches in action.

**Tracking Branches** – the high level summary:



	
  * **Tracking Branches** get their contents from the **git clone, git pull** and **git merge** commands (**git fetch** updates only the **remote tracking branch**).

	
  * **Tracking branches** can be modified by users.

	
  * After the user does a **git commit** in the local **tracking branch**, users can then use **git push** to publish changes from their tracking branch to the remote repository and **remote tracking branches**.


**Remote Tracking Branches** – the high level summary:



	
  * **Remote Tracking Branches** get their contents from the **git clone**, **git pull** and **git fetch** commands (**git merge**updates only the **tracking branch**).

	
  * **Remote Tracking Branches** should not be modified by users (don’t set your git branch to a **remote tracking branch**via **git checkout** and then try to modify the **remote tracking branch**).

	
  * **Remote Tracking Branches** get their contents from the remote repository.


<table border="1" >
<tbody >
<tr >
Branch type
Updated via
Published via
Client Access
</tr>
<tr >

<td >Remote Tracking
</td>

<td >**git fetch, git pull**
</td>

<td >[Not published]
</td>

<td >Read-only
</td>
</tr>
<tr >

<td >Tracking
</td>

<td >**git merge, git pull** (= **git fetch**+**git merge**)
</td>

<td >**git push**
</td>

<td >Read-write
</td>
</tr>
</tbody>
</table>
The examples below demonstrate how the branches are used.


## Example: Our repository




## Example: Our repository


For our examples below, let’s start with a small repository that has 2 commits:

![annotated1](http://www.gitguys.com/gitguys/remote/images_tracking/annotated1.png)

Below is a sample session that created the above commits:

    
    $ <b>echo This is the README file. > README</b>
    $ <b>git add README</b>
    $ <b>git commit -m'Initial commit'</b>
    [master (root-commit) 35ede5c] Initial commit
     1 files changed, 1 insertions(+), 0 deletions(-)
     create mode 100644 README
    $ <b>echo One more line. >> README </b>
    $ <b>git commit -a -m "Added a second line."</b>
    [master b26009d] Added a second line.
     1 files changed, 1 insertions(+), 0 deletions(-)
    $ <b>ls</b>
    README


Assume a git administrator created the remote repository and a different user used **git push** to publish the commits there.

We won’t go into showing how to create the remote repository here since that is already discussed in [Git Remotes: Sharing](http://www.gitguys.com/topics/creating-a-shared-repository-users-sharing-the-repository/). The purpose of this page is to discuss remote repositories from a git _client_ perspective rather than how to set up remote repositories.

Below is a diagram of a remote repository with the two commits, **C1** and **C2** shown:

![remoteonly](http://www.gitguys.com/gitguys/remote/images_tracking/img0.png)




## git clone


The user then types:

    
    $ <b>git clone git://repohost/project1.git</b>


With the above command, git does a number of things, starting out with setting up the **remote-tracking** branch:



	
  * Creates a new **remote-tracking** branch named **origin/master**

	
  * Copies the remote repository from **git://repohost/project1.git** to the new **remote-tracking** branch named**origin/master**.


Git isn’t finished with the **git clone** command, but here is where it is so far:

![clone1](http://www.gitguys.com/gitguys/remote/images_tracking/img1.png)

Note that **remote-tracking** branches are _read-only_ to the local user. For example, the user will not do a **git checkout origin/master** since the user will not be directly editing and adding files to the **origin/master** branch. If the local user wants to poke around and see has changed in the **origin/master** branch, there are other commands available for that.

The **git clone** command isn’t finished. After the remote repository has been copied to the **remote-tracking** branch, git will then continue on with the tasks for the **git clone** command:



	
  * Create a **tracking branch** called **master**

	
  * Set the local user’s **master** branch pointer to point to the same commit as the **remote-tracking** branch’s**origin/master**.


![clone2](http://www.gitguys.com/gitguys/remote/images_tracking/img2.png)

The local git user can now modify the local git repository in the **master** branch.


## Add a file to the local repository


Now that the local git user has a working copy of the repository, they of course add to it:

    
    $ <b>echo '# Beginnings of a Makefile' > Makefile</b>
    $ <b>git add .</b>
    $ <b>git commit -m "Added a Makefile"</b>


With the addition of the third commit (labeled **C3** in the below diagram), the repositories now look like this:

![addcommit](http://www.gitguys.com/gitguys/remote/images_tracking/img3.png)


## Publish The Local Commit


To publish the commit, the user types **git push** which has this effect first: The user’s local **master** branch is published to the remote repository. In our example, the new commit that added the **Makefile** (commit **C3**) is pushed to the remote repository:

![push1](http://www.gitguys.com/gitguys/remote/images_tracking/img4.png)

Next, the **git push** command causes the **remote-tracking** branch to be updated with the latest updates from the remote repository:

![push2](http://www.gitguys.com/gitguys/remote/images_tracking/img5.png)

Note: If the remote repository had been updated by another user with newer commits than the local git user’s repository, then the user would not have been allowed to **git push** to the remote repository. The user would have to first get the new commits from the remote repository and merge the new commits into the local master branch. Then the user would be allowed to push the changes up to the remote repository.

At this point, all 3 branches (the remote repository’s **master** branch, the remote-tracking branch **origin/master** and the tracking branch **master**) all contain the same the same commits and are pointing to commit **C3**.


## Another User Adds To The remote repository


Let’s imagine that another user is also using this new repository and they have pushed a new commit (labled **C4** in the diagram below) to the remote repository:

![remoteadd](http://www.gitguys.com/gitguys/remote/images_tracking/img6.png)

The local git repository won’t get the new commit, **C4** unless the local user uses **git pull** or **git fetch**.


## Fetch the new commit


The local git user would like the new commit to be copied to their local git repository. They decide to do this in a 2-step process with the first step being **git fetch**. **git fetch** copies any new updates from the remote repository into the local**remote-tracking** branch:

    
    $ <b>git fetch</b>


![fetch](http://www.gitguys.com/gitguys/remote/images_tracking/img7.png)

Note at this point, the user’s **master** branch does not yet refer to the new commit: The user’s **master** branch has commits**C1**, **C2** and **C3**, but not **C4** while the **remote-tracking** branch does refer to new commit **C4**.


## Merge in the new commit


The user then merges in the new commit from the **remote-tracking** branch into the user’s working directory. This results in the new commit, **C4** being merged from the **remote-tracking** branch into the user’s local working branch named **master**. For our example, assume the user hadn’t made any other commits to the local **master** branch.

    
    $ <b>git merge origin/master</b>


![fetch](http://www.gitguys.com/gitguys/remote/images_tracking/img8.png)

Note the syntax of the **git merge** command is **git merge branch-to-merge-from**. The command **git merge origin/master**merges any commits from the **origin/master** remote-tracking branch into the user’s current branch, which is the **master**branch.


## An alternative to “git fetch” + git merge”


If the user was sure they wanted to merge (not just fetch) any new updates from the remote repository into the local **master**repository, the user could have typed:

    
    $ <b>git pull</b>


The result is the same as if the user had typed: **git fetch** immediately followed by **git merge origin/master**.

To demonstrate this, let’s go back in time to the point where only the remote repository had the new commit **C4**, like this:

![pull](http://www.gitguys.com/gitguys/remote/images_tracking/img6.png)

The result of a **git pull** is shown below:

![pull](http://www.gitguys.com/gitguys/remote/images_tracking/img9.png)


## Merges Can Add a Commit


If the user had made some commits to their local **master** branch before doing the **git pull** or **git merge**, then the **git pull/merge** would have resulted in a new commit (the merge commit) being created on the user’s local tracking branch.

The user would eventually **git push** the new commits (both the earlier commit they had done and the merge commit) to the remote repository.
