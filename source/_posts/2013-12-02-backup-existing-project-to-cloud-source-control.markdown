---
layout: post
title: "Backup existing project to cloud source control"
date: 2013-12-02 07:12
comments: true
categories: 
---
# Backup existing project to cloud source control #

This artical explains how to backup your existing Visual Studio project to Bitbucket using Mercurial. This may as well apply to any other type of project out there, but I might use some Visual Studio specifics in article.

All you need for this is to download [Mercurial](http://mercurial.selenic.com/).

Use Bitbucket because it has free private repositories.
I prefer to use Mercurial on Bitbucket because I have used it from the beginning and find it more intuitive.
There is great tutorial on how to use Mercurial by Joel Spolsky on [hginit.com](http://hginit.com). I highly recommend starting there.

First, you need to create private repository in Bitbucket. You could create public repository, but I assume you will be backing up the code that you don't want public to see. 

Let's say you have folder structure like this:

    C:
     |_MyProject
      |_DevelopBranch     

You want to backup all code from **DevelopBranch**.

Go to command prompt and locate `C:\MyProject`

Execute `hg clone https://username@bitbucket.org/username/repository-name` to create local repository.<br />
Note to replace `username` with your username on Bitbucket and `repository-name` with your repository name.

You will notice that clone will not execute but it will say that folder is not empty. I renamed the folder to `DevelopBranch2` and executed again. Now, command was successful and I had new folder `DevelopBranch` which contains only onde folder inside `.hg`. I copied the `.hg` folder to DevelopBranch2, deleted DevelopBranch and renamed DevelopBranch2 back to DevelopBranch.

Create `.hgignore` file from [https://gist.github.com/andrijac/4027502](https://gist.github.com/andrijac/4027502) and place it in **DevelopBranch** folder. This will exclude files from eyes of source control that usually are compiled or outputted by build process, very similar like how TFS is ignoring specific files from your source folder (like .dll, .exe, /obj etc.).

Run the command `hg status` in your DevelopBranch to see what has Mercurial detected for adding into source control. If the list is too big (it usually is), use `hg status > output.txt && output.txt` to see all the files added. You will see that files have `?` status on the left side of file path indicating that files are detected in target folder but are not part of repository yet. 

Execute `hg add` to add all the files to repository. 

Now when you run `hg status` you should get `A` status on the left side of file name indicating that files is added.

Next, commit the changes in repository: <br /> 
`hg commit -m "initial commit" -u username`

Next, execute `hg push` which will push (upload) repository changes to Bitbucket repository.
You will be asked to enter password for authentication.
It might take some time to upload all the changes depending on your project size.

And you are done. Next time you want to upload new changes in project:

    hg status
    hg add
    hg commit -m "commit 2013-12-01" -u username
    hg push
