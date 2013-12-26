---
layout: post
title: "Backup existing project to cloud source control"
date: 2013-12-02 07:12
comments: true
categories: 
---

This artical explains how to backup your existing Visual Studio project to Bitbucket using Mercurial. This may as well apply to any other type of project out there, but I might use some Visual Studio specifics in article.

All you need for this is to download [Mercurial](http://mercurial.selenic.com/).

Create repository<a name="create" href="#create" class="anchor">#</a>
-

I use Bitbucket because it has free private repositories. It supports both Mercurial and Git.
I prefer to use Mercurial on Bitbucket because I have used it from the beginning and find it more intuitive to use.
There is great tutorial on how to use Mercurial by Joel Spolsky on [hginit.com](http://hginit.com). I highly recommend starting there.

First, you need to create repository in Bitbucket. When giving a name to repository, try not to use white space because you might have trouble later on when you use command line. Make sure Mercurial is selected as a "Repository type". 
Also, make sure "Access level: This is a private repository" is checked. You could create public repository, but I assume you will be backing up the code that you don't want public to see.

Clone repository<a name="clone" href="#clone" class="anchor">#</a>
-

Let's say you have folder structure like this:

    ~/
      |_MyProject
        |_DevelopBranch     

You want to backup all code from `DevelopBranch`.

Go to command prompt and locate `cd ~/MyProject`

Execute `hg clone https://username@bitbucket.org/username/repository-name` to create local repository.<br />
This command will create local folder with repository.
Note to replace `username` with your username on Bitbucket and `repository-name` with your repository name.
When I did this, my repository name was MyProject-Develop and I did not want my folder to be named like this, but I wanted it to remain `DevelopBranch`. You can keep your folder name by adding folder name at the end of command:
`hg clone https://username@bitbucket.org/username/repository-name foldter-name`. You can create as many respositories as you want, so if you make a mistake, just delete the folder and try again.

If you try to create repository in same folder where your code is already you will notice that cloning will not execute but it will say that folder is not empty. 
Workaround for this is to make a rename existing folder tempraraly, execute clone command, move `.hg` folder to original folder, delete clone folder and rename back the original folder :)
So, I renamed the folder to `DevelopBranch2` and executed clone command. Now, command was successful and I had new folder `DevelopBranch` which contains only one folder inside `.hg`. I copied the `.hg` folder to `DevelopBranch2`, deleted `DevelopBranch` and renamed `DevelopBranch2` back to `DevelopBranch`. Simple, right? :)

Add files to repository<a name="add" href="#add" class="anchor">#</a>
-

Create `.hgignore` file from [https://gist.github.com/andrijac/4027502](https://gist.github.com/andrijac/4027502) and place it in `DevelopBranch` folder. This will exclude files from eyes of source control that usually are compiled or outputted by build process, very similar like how TFS is ignoring specific files from your source folder (like .dll, .exe, /obj etc.).

In command line, go to `cd ~/MyProject/DevelopBranch`. All further commands will be executed from this path.

Run the command `hg status` in your DevelopBranch to see what has Mercurial detected for adding into source control. If the list is too big (it usually is when adding big project), use `hg status > output.txt && output.txt` to see all the files added. You will see that files have `?` status on the left side of file path indicating that files are detected in target folder but are not part of repository yet. 

Execute `hg add` to add all the files to repository. 

Now when you run `hg status` you should get `A` status on the left side of file name indicating that files is added.

Next, commit the changes in repository: <br /> 
`hg commit -m "initial commit" -u username`

Next, execute `hg push` which will push (upload) repository changes to Bitbucket repository.
You will be asked to enter password for authentication.
It might take some time to upload all the changes depending on your project size.

Maintaining the future changes<a name="maintain" href="#maintain" class="anchor">#</a>
-

And you are done. Next time you want to upload new changes in project:

    hg status
    hg addremove
    hg commit -m "commit 2013-12-01" -u username
    hg push

HTH
