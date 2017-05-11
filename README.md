# EOL
Testing configurations for EOL endings in a heterogeneous dev environments

## Intent
The problem of managing EOL for windows and nix development machines is much hard than what it needs to be, but if we do not understand the issue and how to solve them, we'll be forever trying to fix things without understanding what we're doing.

Line endings can be managed by both your editor of choice and git depending on your conifguration.

This repo intends to provide a means to which we can experiment to understand what and where such configuration changes can be made

## Why?
I'm a new contributor on a team whom are primarilty Mac and Ubuntu Desktop. One of the issues we have faced is when running a Terragrunt Plan is Terragrunt hashes the plan/task definiton to detect any changes. If the plan/task is contains differs in line endings, the plan will show the need for a change, when no infra state has actually changed. Therefor an apply will cause a change in the plan, for which there are no logical changes. When another contributor on a nix machine runs a plan,it will change again... 

## Experiments
1. Default settings for EOL for a new repo

**Method**|**Prediction**|**Outcome**
-|-|-|
Add a file via the web interface on GH to see default EOL. Opening file locally in windows with standart git config i.e. `core.autocrlf = true` | After cloning the repo and branch will see the EOL for this file are windows specific `/n /r` or `CRLF` | Both the readme.md and the License file both had windows EOL

2. Adding a .gitattributes file in the root of the repo with the following settings
```
# Set the default behavior, in case people don't have core.autocrlf set.
* text=auto

# Explicitly declare text files you want to always be normalized and converted
# to native line endings on checkout.
*.md text = eol=lf

# Declare files that will always have CRLF line endings on checkout.
*.sln text eol=crlf

# Denote all files that are truly binary and should not be modified.
*.png binary
*.jpg binary
```

then see if we can reset the EOL by doing the following

```
 rm .git/index     # Remove the index to force Git to
$ git reset         # re-scan the working directory
$ git status        # Show files that will be normalized
$ git add -u
$ git commit -m "Introduce end-of-line normalization"
```

**Method**|**Prediction**|**Outcome**
-|-|-|
Making the changes to the git attributes file, then run the re indexing to see if files change their EOL. The setting for `core.autocrlf = true` | After the reindex all file should need to be re added to the staging area, with the only change being the EOL |


 
