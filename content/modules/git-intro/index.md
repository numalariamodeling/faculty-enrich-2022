---
date: "2022-04-29T11:39:00-05:00"
draft: false
title: Git
weight: 4
---

Git is a popular version control software to manage codes, especially in a collaborative environment. Here is a comic from [“Piled Higher and Deeper”](http://www.phdcomics.com) by Jorge Cham regarding version control:
![](https://swcarpentry.github.io/git-novice/fig/phd101212s.png)

Git allows us to make changes to our code, and say if we didn't like the changes, we can always go back to the older versions of the code. In a collaborative environment, our colleagues can make changes to our codes, and we can track who's made what changes to the code, and undo it if necessary. If all of these sounds familiar, yes it does feel like we that track changes function in say Microsoft Word, or the version control function available in Dropbox. The difference is that you can designate a folder as your "Git repository", and you decide which file inside this folder get version controlled.

While Git is a software that lives inside your computer, GitHub is an internet hosting provider that can host your Git repository. For example, a git repository containing the `dtk-tools` Python package is hosted on GitHub. This package is important for us to use EMOD, and is developed by IDM. To obtain the package, we can (1) get granted access to the repository that lives in GitHub, and (2) "clone" the package from GitHub to our local computer. We can then look into the package and find out who has made what changes to the source code of the package. This has made it very easy for coding projects and packages to be shared to others.

Git can get complicated and difficult to use at times... However, in this section, we focus only on the simple steps of getting the packages to your computer.

### Things you'll need
1. Install [GitHub Desktop](https://desktop.github.com/)
2. Get a GitHub account if you don't already have.
3. Get the access to the repository `dtk-tools` and `dtk-tools-malaria` if you don't already have them

### GitHub Desktop
This is an app that provides graphic user interface to GitHub. Note that you do not necessarily need to use this app to interface with Git or GitHub. There are other app such as [Sourcetree](https://www.sourcetreeapp.com/) or commandline that does similar and wonderful job. However for this tutorial, we use GitHub Desktop because it's easier to set things up.

To "download" the package, we "Clone repository" from GitHub, which can be access from "File" at the top bar. If you already connected your GitHub account to the app, you should be able to choose the `dtk-tools` repository in the pop-up window. Then you would specify a "Local path" of your choice (you do not need to add "dtk-tools" to that path.) When you click "Clone", the app would download the package into a folder called `dtk-tools` and put it onto the path you specified.

Do the same thing for `dtk-tools-malaria`!

### More Git?
Of course, there are more to git than what we have just done. During the tutorial, some of the basic function of git, such as `commit`, `push` and `pull` will be demonstrated. These functions will be important when you start developing your own project. Depending on what software you use to code (e.g., PyCharm, VS Code), git functions are often incorporated into them already, so the experience is bound to be slightly different.

If you're planning to use git through command line (e.g., Powershell, Bash etc.) then this is a [good tutorial](https://swcarpentry.github.io/git-novice/) for you to learn more about git.