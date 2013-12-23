---
layout: post
title: "Octopress publishing environment in the cloud"
date: 2013-12-21 10:29
comments: true
categories: 
---

I was looking for a blog platform:

- Good support for developers (sharing code snippets and github gists).
- Highly customizable layout - I don't have to depend on decision of some third party whether they will add a feature that I need
- Ability to backup a blog content.
- Indipendency of hosting platform.
- Ability to recreate a blog anywhere anytime.
- Keeping a track of changes.


Octopress<a name="octopress" href="#octopress" class="anchor">#</a>
-

I sounds too good to be true, but the thing is you can find all these features in [Octopress](http://octopress.org/). Octopress is created from [Jekyll](http://jekyllrb.com/). Some features of Octopress mentioned here also applies to Jekyll. 

####*Good support for developers*
In Octopress, there is [great support](http://octopress.org/docs/) for embeding code in your blog. 
####*Highly customizable layout*
You can customize anything on the blog. Big advantage is that there is no admin user interface that will interfear of changing anything on blog. Some people would consider this as a disadvantage, but I think that to have highly generic interface with ability to change anything in website is very hard to make, it requires constant maintaince. 
If you want to add some feature, this would require you to have user interface to support it. By not having any user interface and counting on user to change the blog, the possabities are endless. Of course this assumes that you know what are you doing which means that Octopress is not for anyone. Read more on this topic in [The best interface is no interface](http://www.cooper.com/journal/2012/08/the-best-interface-is-no-interface).
####*Ability to backup a blog content*
You are writing Octopress articals in plain text Markdown so there is no database to be backed up, there is no special store to take care of, you just backup the files. Ever since Markdown became popular through stackoverflow.com and github, it is becoming more and more accepted in developer community as a common formating syntax for documentation.
####*Indipendency of hosting platform*
Final output of Octopress is static html-css-javascript, which means it can be hosted anywhere.
####*Ability to recreate a blog anywhere anytime*
You are not depended on proprietary software or some closed platform to recreate the output of your blog, you can do that anytime you want.
####*Keeping a track of changes*
To start using Octopress, you need to fork it on github, which means you will immediately have online repository for Octopress. Anything you change or publish can be commited in git. Is there any better way to track changes in blog? I do not think there is.


Nitrous.io<a name="nitrous" href="#nitrous" class="anchor">#</a>
-
I found about [Nitrous.io](https://www.nitrous.io/join/OqTrHcEDHjk) on Joe Marini's post [Tools for Developing on ChromeOS](http://joemarini.blogspot.ae/2013/11/tools-for-developing-on-chromeos.html).
It seemed very interesting because it was a remote view on VM in browser, rendered in HTML. And as we know once rendered in HTML it can be displayed and worked with anywhere, no plugins or third-party software required.
I gave it a try and created Ruby box. What I got is Linux VM running, based on `lsb_release -a`: Ubuntu 12.04.3 LTS.

Once I have setup the box, I went on to [bonus page](https://www.nitrous.io/app#/n2o/bonus) and connected all of my online identities to Nitrous.io to get additional N2O (N2O is like are Nitrous.io currency for upgrading your box). Yes, I am whoring myself to be able to upgrade my box :)

Octopress + Nitrous.io<a name="OctopressNitrous" href="#OctopressNitrous" class="anchor">#</a>
-
I am switching workstations and environments a lot. On the other hand, Octopress requires "things" to be preinstalled things in your local environment. I also switch OSs so having to setup environment on Windows all the time can be time consuming.
I figured I could have a always Octopress ready environemnt on Nitrous.io, so I gave it a try. This is only way to have Octopress always avaialble to you where ever you are. The trend how cloud computing is progressing, I think that personal workstation on remote VM [will be more and more pratice](http://yieldthought.com/post/12239282034/swapped-my-macbook-for-an-ipad).


Setting up<a name="setup" href="#setup" class="anchor">#</a>
-

***
#### In summary
These are the steps that we will need to go through to setup a blog:

- Register on Github
- Register on Nitrous.io
- Change a Ruby version
- Clone and setup Octopress

***
I have chosen to host the blog on github, which means that address will be [username].github.io. If you want to have it also on github, make sure you like the username you choose for yourself.

I am hosting this blog on github and I will describe here how to host it there. You do have [other options available](http://octopress.org/docs/deploying/), and since blog is generated in plaing html-css-javascript, it can be hosted anywhere after all.

Register on [Nitrous.io](https://www.nitrous.io/join/OqTrHcEDHjk) and create a Ruby box. I am still not sure what are the differences between other boxes, for example I have node.js installed on Ruby box so I'm not sure what is special about node.js box.

Box will be created in a few seconds and ready to roll. You will see `workspace` folder in folder tree on a side.

Further, I have used [Octopress setup](http://octopress.org/docs/setup/).

Git should be already installed, but you can check:

    > git --version                                                                                                                                                                                               
    git version 1.8.4.3

Check Ruby version:

    > ruby --version                                                                                                                                                                                              
    ruby 2.0.0p247 (2013-06-27 revision 41674) [x86_64-linux] 

Ruby version required for Octopress is 1.9.3. So, we need to change a Ruby version. I used `rbenv`:
    
    > git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
    > echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
    > echo 'eval "$(rbenv init -)"' >> ~/.bashrc
    > git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
    > source ~/.bashrc
    
 At this point, you might need to restart a console. Openning new console should do it.
 Install Ruby 1.9.3:
 
     > rbenv install 1.9.3-p0
    
Clone Octopress from Github. I created a folder inside workspace folder:

    > git clone git://github.com/imathis/octopress.git ~/workspace/octopress
    > cd ~/workspace/octopress   
    
Setup use of Ruby 1.9.3 in local folder (your path should be now ~/workspace/octopress):
    
    > rbenv local 1.9.3-p0
    > rbenv rehash

Install dependencies:

    > gem install bundler
    > rbenv rehash
    > bundle install

Install default Octopress theme:

    > rake install


### Deploying to Github Pages <a name="githubdeploy" href="#githubdeploy" class="anchor">#</a>

As I said before, if you are planning to deploy to GitHub pages, your address will be `http://username.github.io`. 
You need to create a repository which will be called `username.github.io`. 
[Here are detail instructions](http://octopress.org/docs/deploying/github/), but here I will give summary of all the steps you need to do.

Run: 

    > rake setup_github_pages

It will ask you for repository url, which it suppose to be `https://github.com/username/username.github.io.git`.
Github recomments https over ssh, [why?](http://stackoverflow.com/a/11041782/84852)

Make sure are have github email and username configured:

    > git config --global user.email "your email"
    > git config --global user.name "username"

Next, push the current source to `source` branch:

    > git add .
    > git commit -m "commit message"
    > git push origin source

Next, generate and deploy.

    > rake generate
    > rake deploy

To clarify, you will have 2 branches in repository:

- `source` branch holding the source of the blog, which you are using for generation.
- `master` branch which will be static generated content. Github pages are configured so anything that appears in repository called `username.github.io` in `master` branch will appear on github page url.

`rake generate` will generate static content locally in `_deploy/` folder.
`rake deploy` will push the changes to `master` branch.

At this point, you are done. It will take few minutes for github to publish the page for the first time, later on it will be published quicker.

Updating the blog <a name="updating" href="#updating" class="anchor">#</a>
-

- git coomit i push u source
- novi post
- configuratcija bloga
- link do dokumentcije
- previewing









