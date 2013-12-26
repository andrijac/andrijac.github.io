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
- Ability to back up blog content.
- Independency of hosting platform.
- Ability to recreate a blog anywhere anytime.
- Keeping a track of changes.


Octopress<a name="octopress" href="#octopress" class="anchor">#</a>
-

I sounds too good to be true, but the thing is you can find all these features in [Octopress](http://octopress.org/). 

*[octopress.org](http://octopress.org/)* :
> Octopress is a framework designed by [Brandon Mathis](http://brandonmathis.com/) for [Jekyll](http://github.com/mojombo/jekyll), the blog aware static site generator powering Github Pages

Some features of Octopress mentioned here also apply to Jekyll. 

####*Good support for developers*
In Octopress, there is [great support](http://octopress.org/docs/) for embedding code in your blog. 
####*Highly customizable layout* (review)
You can customize anything on the blog. Big advantage is that there is no admin user interface that will interfere of changing anything on blog. Some people would consider this as a disadvantage, but I think that to have highly generic interface with ability to change anything in website is very hard to make and to maintain. 
If you want to add a feature, it would require you to have user interface to support it, which also mean additional user interface to maintain along with this feature. By not having any user interface and counting on user to change the blog manually, the possibilities are endless.<br />
Of course this assumes that you know what you are doing which means that Octopress is not for anyone. <br />
Read more on this topic in [The best interface is no interface](http://www.cooper.com/journal/2012/08/the-best-interface-is-no-interface).
####*Ability to back up blog content*
You are writing Octopress articles in plain text Markdown so there is no database to be backed up, there is no special data store to extract data from, and you just back up the files. Ever since Markdown became popular through Stackoverflow.com and Github, it is becoming more and more accepted in developer community as a common formatting syntax for documentation.
####*Independency of hosting platform*
Final output of Octopress is static html-css-javascript, which means it can be hosted anywhere.
####*Ability to recreate a blog anywhere anytime*
You are not depended on proprietary software or some closed platform to recreate the output of your blog, you can do that anytime you want.
####*Keeping a track of changes*
To start using Octopress, you need to fork it on Github, which means you will immediately have Git repository for Octopress. Anything you change or publish can be committed in Git. Is there any better way to track changes in blog? I do not think so.


Nitrous.io<a name="nitrous" href="#nitrous" class="anchor">#</a>
-
I found about [Nitrous.io](https://www.nitrous.io/join/OqTrHcEDHjk) on Joe Marini's post [Tools for Developing on ChromeOS](http://joemarini.blogspot.ae/2013/11/tools-for-developing-on-chromeos.html).
It seemed very interesting because it was a remote view on VM in browser, rendered in HTML. And as we know once rendered in HTML it can be displayed and worked with anywhere, no plugins or third-party software required.
I gave it a try and created Ruby box. What I got is Linux VM running:
{% codeblock %}
lsb_release -a
#Ubuntu 12.04.3 LTS.
{% endcodeblock %}

Once I have setup the box, I went on to [bonus page](https://www.nitrous.io/app#/n2o/bonus) and connected all of my online identities to Nitrous.io to get additional N2O (N2O is like are Nitrous.io currency for upgrading your box).

Octopress + Nitrous.io<a name="OctopressNitrous" href="#OctopressNitrous" class="anchor">#</a>
-
I am switching workstations and environments a lot. On the other hand, Octopress requires some stuff to be preinstalled in your local environment. I also switch OSs so having to setup environment on Windows all the time can be time consuming.
I figured I could have an always Octopress ready environment on Nitrous.io. This is only way to have Octopress always available. The trend how cloud computing is progressing, I think that personal workstation on remote VM [will be more and more practice](http://yieldthought.com/post/12239282034/swapped-my-macbook-for-an-ipad).


Setting up<a name="setup" href="#setup" class="anchor">#</a>
-


#### In summary
These are the steps that we will need to go through to setup a blog:

- Register on Github
- Register on Nitrous.io
- Change a Ruby version
- Clone and setup Octopress


I have chosen to host the blog on Github, which means that address will be [username].github.io.

I am hosting this blog on Github and I will describe here how to host it there. You do have [other options available](http://octopress.org/docs/deploying/), and since blog is generated in plain html-css-javascript, it can be hosted anywhere.

Register on [Nitrous.io](https://www.nitrous.io/join/OqTrHcEDHjk) and create a Ruby box. I am still not sure what are the differences between other boxes, for example I have node.js installed on Ruby box so I'm not sure what is special about node.js box.

Box will be created in a few seconds and ready to roll. You will see `workspace` folder in folder tree on a side.

Further, I have used [Octopress setup](http://octopress.org/docs/setup/).

Git should be already installed, but you can check:
{% codeblock %}
git --version
# git version 1.8.4.3
{% endcodeblock %}

Check Ruby version:
{% codeblock %}
ruby --version
# ruby 2.0.0p247 (2013-06-27 revision 41674) [x86_64-linux] 
{% endcodeblock %}

Ruby version required for Octopress is 1.9.3. So, we need to change a Ruby version. I used `rbenv`:
{% codeblock %}
git clone https://github.com/sstephenson/rbenv.git ~/.rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
git clone git://github.com/sstephenson/ruby-build.git ~/.rbenv/plugins/ruby-build
source ~/.bashrc
{% endcodeblock %}

At this point, you might need to restart a console. Opening new console should do it.
Install Ruby 1.9.3:
{% codeblock %}
rbenv install 1.9.3-p0
{% endcodeblock %}

Clone Octopress from Github. I created a folder inside workspace folder:
{% codeblock %}
git clone git://github.com/imathis/octopress.git ~/workspace/octopress
cd ~/workspace/octopress   
{% endcodeblock %}

Setup use of Ruby 1.9.3 in local folder (your path should be now ~/workspace/octopress):
{% codeblock %}
rbenv local 1.9.3-p0
rbenv rehash
{% endcodeblock %}

Install dependencies:
{% codeblock %}
gem install bundler
rbenv rehash
bundle install
{% endcodeblock %}

Install default Octopress theme:
{% codeblock %}
rake install
{% endcodeblock %}


### Deploying to Github Pages <a name="githubdeploy" href="#githubdeploy" class="anchor">#</a>

As I said before, if you are planning to deploy to Github pages, your address will be `http://username.github.io`. 
You need to create a repository which will be called `username.github.io`. 
[Here are detail instructions](http://octopress.org/docs/deploying/github/), but here I will give summary of all the steps you need to do.

Run: 
{% codeblock %}
rake setup_github_pages
{% endcodeblock %}

It will ask you for repository url, which it supposed to be `https://github.com/username/username.github.io.git`.
Github recommends HTTPS over SSH, [why?](http://stackoverflow.com/a/11041782/84852)

Make sure are have Github email and username configured:
{% codeblock %}
git config --global user.email "your email"
git config --global user.name "username"
{% endcodeblock %}

<a name="commit"></a>Next, push the current source to `source` branch: 
{% codeblock %}
git add .
git commit -m "commit message"
git push origin source
{% endcodeblock %}

<a name="deploy"></a>Next, generate and deploy.
{% codeblock %}
rake generate
rake deploy
{% endcodeblock %}

To clarify, you will have 2 branches in repository:

- `source` branch holding the source of the blog, which you are using for generation.
- `master` branch which will be static generated content. Github pages are configured so anything that appears in repository called `username.github.io` in `master` branch will appear on your Github page.

`rake generate` will generate static content locally in `_deploy/` folder.
`rake deploy` will push the changes to `master` branch.

At this point, you are done. It will take few minutes for Github to publish the page for the first time, later on it will be pushed much quicker.

Blogging<a name="blogging" href="#blogging" class="anchor">#</a>
-
Helpful links:

- [Cofiguration](http://octopress.org/docs/configuring/)
- [Blogging Basics](http://octopress.org/docs/blogging/)

Some quick notes:

- Configuration file is `_config.yml`. You can configure all global settings there like title, description, add Github account, Disqus account etc.
- Create a new post: `rake new_post["post title"]` this will create a new markdown document in `source/_posts`.
- Preview the blog: 
{% codeblock %}
rake generate   # Generates posts and pages into the public directory
rake watch      # Watches source/ and sass/ for changes and regenerates
rake preview    # Watches, and mounts a webserver at http://localhost:4000
{% endcodeblock %}
You will be able to see your blog on port 4000. In Nitrous.io, go to menu `Preview > Port 4000` and it will open a blog preview in new tab

- To commit changes to Git, use the same [git commands](#commit) like in initial commit.
- Once you are ready to publish, use the same [rake commands](#deploy) for publish like you did initially.

HTH