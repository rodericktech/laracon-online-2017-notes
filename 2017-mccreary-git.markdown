You Don't Know Git
------------------

_Jason McCreary, Laracon Online 2017_

### Introduction

Many git commands will be covered in a shotgun approach.

### Let's Git Started            ...

**Laravel Shift** is built on git. In the course of running Shift, Jason ran into a lot more
questions about git then about Laravel / upgrading. We often use git while knowing very little
about it.

We need to find a **Git Master** to help us work through our issues...

[Jason's Git Master, Richard]

Git is an empowering tool, part of so many workflows, and it behooves us to understand and master
this toolkit.

### Unfamiliar Commands

**git add**

A lot of us use this one incorrectly.

Common usages (basically grab-all commands):

~~~~
git add .
git add -A
git commit -am ''
~~~~

How about this?

~~~~
git add -p      // goes through all the hunks for staging
~~~~

The -i switch lists all the untracked files - use * to add all:

~~~~
git add -i      // interactive - parent prompt of patch
~~~~

**git-commit**

Let's say we've made a WIP commit. And we want to add to it rather than make a new one.

~~~~
git commit --amend
~~~~

You can run git log and verify that it rolled the new changes into the previous commit. One point
to notice: the commit SHAs change after an --amend. If you shared a previous commit with others,
this can screw you up and you'll have to deal with it via merge / rebase. Ideal for work that
isn't shared yet.

**git stash**

This it the poor man's commit. It makes a sort of WIP commit and puts it on an internal stack and
'out of the way', temporarily removing the changes from the working area.

~~~~
git stash --include-untracked
git stash list
~~~~

Note that the stashes aren't real commits and don't have SHAs, though you can save them with a
message (not recommended - make real commits instead).

**git push**

This will push things to an existing branch or create one if it doesn't exist:

~~~~
git push origin master:staging
~~~~

**git rebase**

Particularly, git rebase -i (interactive). You pass it a SHA of the first commit that you _don't_
want to be included in the rebase.

The command will put you in an editor, sort of like a git log; you set the commands you want to
run on each commit, and then it runs them in order and prompts for interaction where necessary.

~~~~
git rebase -i <SHA>
~~~~

Note that this also changes the commit SHAs like --amend, and is thus history-breaking.

**git cherry-pick**

If you just want one commit to pull to master, you can use this 'surgical' command:

~~~~
git cherry-pick <SHA>
~~~~

**git bisect**

Let's say you have a really long commit history, and somewhere, you got a bug. You don't know
when it was introduced and it's going to be very hard to track down. You _do_ know that way
back when, there was a commit where the code was good (i.e., free of the bug).

~~~~
git bisect <bad SHA> <good SHA>
~~~~

This will move you around in the commit history and ask if the problem is still there or not until
the needle in the haystack is found.

**git reflog**

Let's say you messed up the rebase.

~~~~
git reflog
~~~~

This will show you all the references for recent activity. You can find a SHA in the list and
checkout that point where you want to return to. This puts you in a detached HEAD state (not on
a branch). This is not for regular workflow, more for weird 'oh crap' moments.  Make a habit
of creating a backup branch in your regular flow.

### Common Workflows

**Git Flow**

This is the parallel, multiple-long-running branch model. Branches for develop, release, hotfixes,
features... but the trouble with this method is what to merge where. Usually this is more complex
than what you need. A great example of a _good_ use of this flow is the Laravel project.

**GitHub Flow**

This is a more 'modern' approach, and sort of branded (obviously). It's a strategy that employs
a single, long-running **master** branch and features are all developed on separate branches
and then merged back in. It is a simpler process and is typically more likely to be useful.

You work quickly on features and avoid most of the merge-hell issues of Git Flow.

### Helpful Shorthands / Shortcuts

**HEAD:**

~~~~
git log --oneline -1 HEAD~1     // one commit before HEAD
git log --oneline -1 HEAD^      // parent commit
git log --oneline -1 HEAD^^
git log --oneline -1 HEAD~2^    // mix & match!
git log --oneline -1 <SHA>~
~~~~

Always want to push the topmost commit?

~~~~
git push origin HEAD
~~~~

**Branch traversal:**

~~~~
git checkout -      // moves to branch you were previously on
~~~~

**Aliases:**

~~~~
git config --get-regexp alias       // show your aliases
git wtf                             // :)
~~~~

Often, git aliases lead to unnecessary obfuscation. Careful with them. If you're doing pair
programming, swapping machines, etc. it can be really strange to alias common commands for a
variety of reasons.

Tab completion is _much_ better than aliasing for common keystroke-saving purposes.

**Prompt:**

~~~~
Directory - branch name - status
~~~~

Careful of 'terminal envy'...

**git open**

A script of Jason's that was turned into an npm package - it can figure out where a given repo
is and do a few other handy shortcut things.

### Getting Git

Jason's 'Getting Git' series of over 50 videos.

**20% off at https://gettinggit.com/laracon-online**

### Questions

**Q. Do you have plans to build Shift for Spark?**

A. It already works. :D Spark even has its own internal 'shift' process.

**Q. If there are conflicts during git stash pop...?**

A. Work through it just like a normal merge conflict (diff tools, etc.).

**Q. Recommended changes to git config defaults?**

A. Sort of like aliases... Jason doesn't want to make too many customizations. He doesn't have
any specific suggestions.

### Bonus

From @austinkregel in Slack chat, a pretty git log:

~~~~
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
~~~~
