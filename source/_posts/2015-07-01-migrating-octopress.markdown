---
layout: post
title: "Migrating Octopress"
date: 2015-07-01 06:49
comments: true
categories: 
---

Quick notes on how to migrate existing Octopress setup (version 2) to new environment.<br>
Here, I am migrating from [lite.nitrous.io](lite.nitrous.io) (now obsolete) to [pro.nitrous.io](pro.nitrous.io).

##Steps

- `clone` source branch => `git clone <url> --branch source <folder-name>`
- Check Ruby version. It should be 1.9.3
- `cd` in Octopress work folder:
- `gem install bundler`
- `bundle install`

If not already set: <br>
`git config --global user.email "name@example.com"`<br>
`git config --global user.name "your\_userusername"`

- `rake setup_github_pages`
- reset HEAD in \_deploy folder: <br>
`git fetch`<br>
`git reset --hard origin/master`

Try:

- `rake generate`
- `rake preview`
- `rake deploy`