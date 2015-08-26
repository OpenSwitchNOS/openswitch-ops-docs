---
title: Guidelines for commit messages
layout: document
---

# Guidelines for commit messages

Commit messages for change sets are recorded in the git repository when the changes are merged. Keeping with a simple, consistent format for these messages makes it easier for other users (and tools) to understand the changes.

The following excerpt is from the [Git Pro, 2nd Edition](http://git-scm.com/book/ch5-2.html#commit_guidelines) book, chapter 5. We gratefully acknowledge the book's [CC BY-NC-SA 3.0](http://creativecommons.org/licenses/by-nc-sa/3.0/legalcode) license which permits replication without providing endorsement.

> Getting in the habit of creating quality commit messages makes using and collaborating with Git a lot easier. As a general rule, your messages should start with a single line that’s no more than about 50 characters and that describes the changeset concisely, followed by a blank line, followed by a more detailed explanation. The Git project requires that the more detailed explanation include your motivation for the change and contrast its implementation with previous behavior – this is a good guideline to follow. It’s also a good idea to use the imperative present tense in these messages. In other words, use commands. Instead of “I added tests for” or “Adding tests for,” use “Add tests for.” Here is a template originally written by Tim Pope:

```
Short (50 chars or less) summary of changes

More detailed explanatory text, if necessary.  Wrap it to
about 72 characters or so.  In some contexts, the first
line is treated as the subject of an email and the rest of
the text as the body.  The blank line separating the
summary from the body is critical (unless you omit the body
entirely); tools like rebase can get confused if you run
the two together.

Further paragraphs come after blank lines.

  - Bullet points are okay, too

  - Typically a hyphen or asterisk is used for the bullet,
    preceded by a single space, with blank lines in
    between, but conventions vary here
```
>If all your commit messages look like this, things will be a lot easier for you and the developers you work with.

**Note**: there is no period ('.') at the end of the summary (first) line.

## Contents

<!-- TOC depth:6 withLinks:1 updateOnSave:1 orderedList:0 -->

- [Git Basic commands](#git-basic-commands)
- [Day to day Git workflow](#day-to-day-git-workflow)
- [Git commit message guidelines](#git-commit-message-guidelines)
	- [Subject](#subject)
	- [Body](#body)
	- [Use cases](#use-cases)
	- [Bug fixes](#bug-fixes)
	- [Changes](#changes)
- [Gitchangelog](#gitchangelog)
<!-- /TOC -->

## Git Basic commands

In your Virtual Machine in a Unix terminal, or whatever environment you are using to access your local repository, execute:

- Check how many branches you have
  - `git branch -a`

You should display something like:

```bash
* master
  remotes/origin/HEAD -> origin/master
  remotes/origin/anfred
  remotes/origin/astuad
  remotes/origin/cespedec
  remotes/origin/delgadoj
```

Note the * above, that asterisk denote which is the "current" branch. You should have a master branch and a "personal" branch, in this example, my personal branch is "garitaci". The other branches present correspond to other users personal branches.

- If not in the master branch, check out the master branch and perform a git pull.
  - `git checkout master`
  - `git pull`

  Thus learn here, the `git checkout` command switches between branches, while the `git pull` commmand updates the selected branch.

- Using `git status`:
Move away from your master branch by executing:
  - `git checkout "username"` (Ex. git checkout garitaci)
  - Browse to any file you must wish to change. Change that file.
  - `git status`

  The git status command above will show you what files have been changed. There are several categories under `git status` command. These are:
  - _Untracked files_: Files that you have created but are not tracked yet by Git, thus they are not under version control. If you have untracked files you want to add to the project (our API) use "git add file" (include complete path)
  - _Files not ready for commit_: This files have been changed and are under Git version control (locally), but you have not added the changes in them to a commit. A commit means files are ready to join the project. If you have changed files locally and want to definitely add these changes or commit them to the repository, then use `git add file` (include complete path) to move the file to the "files ready to commit" status.
  - _Files ready to commit_: This files are ready to commmit, this means, once you execute `git commit` command, this file will permanently be added to the current branch you are working on.

Execute `git status`  as many times needed to check which files you have changed and on which status the fall.

- Using `git commit` and `git push`:

  When you have changed files and used "git add path/filename" to add them to the "files ready to commit" status (use git status to check they are ready to commit) then you can execute the git commit command. The usual syntax is:

  - `git commit -m "Commiting my first change in the repository"`

    Note the -m option, this means you leave a message (and you must) on what you are committing to the repo. After a commit your file has been officially added to your local branch but has not been added to the "online" repository (for all others to see, in the master or your own branch!). For others to see your local changes, you must to a git push.
  - `git push`

    This will add changes to the current branch, and all others will be able to "git pull" your changes unto their machines.

**Note:** You must work on your personal branch before doing a `git push` on the master. To do a git push on the master please read the next section and also check this wiki [Compile Workflow](https://asic-api.rose.hp.com/wiki/index.php/Compile_Workflow). Remember you can only push to the master when everything works (compiles, links, etc)!

## Day to day Git workflow

After you have learned to use git, you will follow a general method for updating the master and personal branches.

```bash
git checkout master              # go to main branch
git pull                         # update main branch
git checkout "personalBranch"    # go to your personal (also called 'topic') branch
git rebase master                # apply main branch’s changes your topic branch
git push origin "personalBranch" # apply to upload your personal branch changes to remote personal branch repo
git checkout master              # go back to main branch
git merge "personalBranch"       # apply changes from topic branch to main branch
git push origin master           # update main repository
```

**Notes:**
1. Branches will differ only when you commit your changes.
2. There is only one working tree. This means: If you switch between branches without committing those changes will still be in the next branch.

## Git commit message guidelines
The commit messages are of great importance on a development process. They help the developers and final users to understand why change were made. Therefore it is important to follow these guidelines. The first thing to take into account is that ALL our commit messages must have a subject and a body, explained in detail in the next sections.
The commit messages should been write impersonal. Use verbs like _Add_, _Fix_, _Change_, _Update_ or _Implement_. **Don't** use _Added_ or _Adding_, for example. Another important point is that each commit must change/add/fix only one thing. Keep in mind that each published commit, will live forever and sometime when you go back to the project history for some issue, it could save you the day due to a good hint in an informative commit message.

### Subject

The first line of the commit message is known as the subject. It describes the change briefly and helps reviewers to see at a glance what the commit is about. It should be no more than 50 characters (must be less than 80). Since we are using gitchangelog (a open source tool that generates changelogs) the subject must have the following format:

```
act: aud: Here comes the subject of the commit @tag
```

Where _act_ (action) classifies the type of changes made in the current commit and could be:
- 'new' is used to say that new features were added to the code
- 'fix' is used to say that there was a bug fixed
- 'chg' is used to say that changes (refactor, small improvement, cosmetic changes) have been made to the code.

The keyword _aud_ determines the intended audience of the commit message could be:

- 'dev' the commit message is intended to developers
- 'usr' the commit message is intended to the final user

If a subject has a tag the commit will not appear in the changelog. _tag_ could be:

- 'minor' is a minor change that you don't want to show in any changelog
- 'cosmetic' is cosmetic change like delete white spaces, indent the code or edit the comments; and you don't want this commit to appear in the any changelog
- 'refactor' is refactor you don't want to appear in the any changelog
- 'wip' is work in progress you don't want to appear in any changelog

**All the commit messages with the audience usr are parsed to generate the release notes. Be extra careful with those messages!.**

### Body

Use the body of the commit message to describe your commit in detail. And follow these guidelines:

- Separate the body from the subject with an empty line.
- Give an overview of why you're committing this change.
- Don't use bullet points.
- What the commit changes.
- Any new design choices made.
- Areas to focus on for recommendations or to verify correct implementation.
- Any research you might have done.
- Wrap the body of the message between 70 and 100 characters.

### Use cases

The general format of a commit message should look like:

```
act: aud: Short description of the work done in this commit

Explanation of the work done in this commit. This explanation
must explain in detail the change. Don't use bullet points.
```

### Bug fixes

If the bug #1043 has been fixed and you want it to be in the release notes then its commit message should look like:

```
fix: usr: Fix Bug #1043. Now Action_set could increase size.

Pass write_to_gpt algorithm to write first on high GPT half and write to
hardware when low GPT half is written. gpt_location_idx_get is giving a
relative position to the action_set label in UCO.
```

If the same bug has been fixed but you don't want it in the release notes then you'll have to change _usr_ to _dev_

```
fix: dev: Fix Bug #1043. Now Action_set could increase size.

Pass write_to_gpt algorithm to write first on high GPT half and write to
hardware when low GPT half is written. gpt_location_idx_get is giving a
relative position to the action_set label in UCO.
```

If more than one bug has been fixed with the same change then its commit message should look like:

```
fix: usr: Fix Bugs #1043, #1045 and #1056. Now Action_set could increase size.

Pass write_to_gpt algorithm to write first on high GPT half and write to
hardware when low GPT half is written. gpt_location_idx_get is giving a
relative position to the action_set label in UCO.
```

**If each bug requires a separate change then _don't_ put all them together in the same commit, you should do a commit for each bug.**

### Changes

You have made a small improvement on the pv_pm_neo_indirectLookUpEntry and you want to share it with you coworkers but not with the final user (release notes). Your commit message should look like:

```
hg: dev: Change pv_pm_neo_indirectLookUpEntry SPs handling (no ucx support)

Move ind_lut entries for extension mode to entries 125 & 126
(now INDLUT_MODE_ENTRY on pm.h can be used only to change it)
```

After you did this commit you realize that the name of a non-important variable should be change. The message of the commit changing the name of the variable should look like:

```
chg: dev: Change pv_pm_neo_indirectLookUpEntry variable name @minor

Change the name of the variable the variable old_name that stored the
a previous state to new_name.
```

Maybe you need to edit some comments in the code. Then your commit message should look like:

```
chg: dev: Edit comments of pv_pm_neo_indirectLookUpEntry @cosmetic

Edit the comments of the pv_pm_neo_indirectLookUpEntry. Explain the usage
of some variables inside the function function_name.
```

Or you need to refactor the code and you don't want that commit to appear in any changelog. Then your commit message should look like:

```
chg: dev: Refactor pv_pm_neo_indirectLookUpEntry @refactor

Change the way the loop works to save clock cycles.
```

All the commits with a commit message subject with a tag like **@minor**, **@cosmetic** and **@refactor** won't appear in any changelog.

**Don't include many different changes into one commit. Each change must have it's separate commit.**

## Gitchangelog

Gitchangelog is an open source project that parse the git log to generate formated changelogs. We use this tool to autogenerate the release notes. The API repository has a ChangeLog file updated when each release is done. If you want to see the current ChangeLog (changes since last release plus the unreleased changes) you can run the gitchangelog script from the API repository. Before running the script you'll have to install mako:

```bash
sudo apt-get install python-mako
```

To run the script from the repository:

```bash
python <pvapi path>/tools/scripts/gitchangelog/gitchangelog.py
```

If you want to install gitchangelog on your computer run:

```
sudo apt-get install python-setuptools
cd <pvapi path>/tools/scripts/gitchangelog
sudo ./autogen.sh && sudo python setup.py install
```

Now you can simply run

```
gitchangelog
```

Inside the API repository and you'll see the current changelog.

