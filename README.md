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
*.md text eol=lf

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
Making the changes to the git attributes file, then run the re indexing to see if files change their EOL. The setting for `core.autocrlf = true` | After the reindex all file should need to be re added to the staging area, with the only change being the EOL | No files were changed and EOL remained the same. Also new files had EOL `crlf`

A change to the attirbutes file was made as below

```
# Set the default behavior, in case people don't have core.autocrlf set.
*    text=auto

# Denote all files that are truly binary and should not be modified.
*.png binary
*.jpg binary
 ```

**Method**|**Prediction**|**Outcome**
-|-|-|
Make a change to the .gitattributes file as above. Re-run the re-index and see the outcome | That the files wil have EOL `crlf` | This was the outcome, but this also converted files with `lf` to `crlf` and stated the following message `warning: LF will be replaced by CRLF in README.md. The file will have its original line endings in your working directory.`
 

Make the following changes to the .gitattributes file
```
# Set the default behavior, in case people don't have core.autocrlf set.
*    text=auto eol=lf

# Denote all files that are truly binary and should not be modified.
*.png binary
*.jpg binary
 ```
 
 **Method**|**Prediction**|**Outcome**
-|-|-|
Make the above changes to the .gitattributes file and commit. Then reset the git index | All files should be converted to EOL lf in both the index and working dir | Files created with EOL `lf` were modified in the working dir to be `lf`. Those created in windows with EOL `crlf` were note modifed in the working directory. It seems that EOL are converted in the git dB, but are left unchanged in the working dir

Make all files in working dir EOL `lf` with some tool/script/text editor 

 **Method**|**Prediction**|**Outcome**
-|-|-|
With the same .gitattributes file , i.e. `* text=auto eol=lf` set all the files in the working dir to EOL `lf` and commit changes.| Any files note created in windows should have original EOL `lf`, and any ones created on windows system should be `lf` as well. So all file in working dir and dB should be EOL `lf` for current and new branch | the working dir staed EOL `lf` and any new branch stayed `lf` as well.

## Conclusions
So, it seems that the biggest lesson is the difference between the working dir and the git dB. it seems the .gitattributes file will help preserve EOL from the repo, but any new files created on a windows system (or any other that does not use EOL `lf` will need to have that changed in the working dir.

EOL should be a solved problem, but it is not, and havin a mechanism/agreement/contract to deal with this is highly reccomended. As I found, there is a lot of dark magic between the .gitattributes file,config settings and test editors of choice. Please feel free to use the experiments in this repo to further your understanding what solution may work best for you.

If you have any differences in you outcomes, please send a PR

