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
