---
comments: true
date: 2012-12-16 08:48:53+00:00
layout: post
title: Git submodules
categories: [android, git, library, submodule]
---

When you need to add a library to a project in git repository you can use git submodules for it. Read in this post how you can do it.

<!-- more -->

For example, recently I added ActionBarSherlock to one of my projects. ActionBarSherlock cannot be included as a jar file because it has its own resources. Resources are not included in jars. So to include ActionBarSherlock you have to add it as Android library. To do it do the following.

<ol>
<li>
Go to the project folder and issue the following command:

``` sh
$ git submodule add git://github.com/JakeWharton/ActionBarSherlock.git
```
</li><li>
Wait until the library downloaded and then modify `.gitmodules` file by adding `"ignore = dirty"` line to just added submodule

``` sh
$ cat .gitmodules
[submodule "ActionBarSherlock"]
  path = ActionBarSherlock
  url = git://github.com/JakeWharton/ActionBarSherlock.git
  ignore = dirty
```

By doing this you tell git that you do not want to know whether the library project (git submodule) was modified.

</li><li>
Commit your changes.
``` sh
$ git commit -m "Added ActionBarSherlock as git submodule"
```
</li><li>
And if you are using eclipse just import ActionBarSherlock project.  
{% img center /images/2012/12/16/eclipse-import.png 'Import project' %}

Choose "Existing Android Code Into Workspace" during import and check the library. There is no need to import ActionBarSherlock samples.  
{% img center /images/2012/12/16/eclipse-import-project-1.png 'Existing Android code into workspace' %}

{% img center /images/2012/12/16/eclipse-import-project-2.png 'Check library' %}

And then tell your project that you want to use ActionBarSherlock as a library.  
{% img center /images/2012/12/16/eclipse-project-properties.png 'Project properties' %}
</li>
</ol>

If you are cloning some project with git submodules within it you need to tell git about it.

Just run the following commands after checkout:

``` sh
$ git submodule init
$ git submodule update
```

To stop using a submodule just follow these steps which I found [here](http://stackoverflow.com/a/1260982):

1. Delete the relevant section from the `.gitmodules` file.  
2. Delete the relevant section from `.git/config`.  
3. Run `git rm --cached path_to_submodule` (no trailing slash).  
4. Commit and delete the now untracked submodule files.
