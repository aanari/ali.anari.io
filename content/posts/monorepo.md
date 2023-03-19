---
title: "Monorepo migration"
date: 2022-10-20
draft: false
description: "Useful script"
tags: [git, shell]
---

## Overview

A monorepo is a version-controlled code repository that holds many projects (the most famous example being [google3](https://research.google/pubs/pub45424/), clocking in at over 80 TB).

There are several benefits to a monorepo architecture compared to a multirepo architecture, including:

* **Visibility:** Everyone can see everyone else’s code. 
* **Simpler dependency management:** Sharing dependencies is trivial. 
* **Single source of truth:** No more dependency hell.
* **Consistency:** Clearer code quality standards and style guides.
* **Shared timeline:** Breaking changes are exposed immediately to teams.
* **Atomic commits:** Large-scale refactoring is much easier.
* **Unified CI/CD:** Re-use build and deployment pipelines for every project.

However, if you've already invested a lot of time and effort into setting up multiple repos, the cost of switching to a monorepo later may seem daunting. Thankfully, we can use some clever scripting to make this task much easier.

## Scripting


First, decide on the folder structure that you want your monorepo to have. Let's say that you have a separate repo called "lib", and you want all commits within "lib" to also be prefixed with the `lib/` path once migrated over to the monorepo.

To achieve this, you'll need to use a `git filter-branch` command within your "lib" repo:

```bash
git filter-branch --index-filter 'git ls-files -s | sed "s-\t\"*-&lib/-" | GIT_INDEX_FILE=$GIT_INDEX_FILE.new git update-index --index-info && mv "$GIT_INDEX_FILE.new" "$GIT_INDEX_FILE"'
```

Next is a helper script that I wrote, called `monorepo.sh`, that can make migrating the commits and branches much easier. Copy this script to your project folder that's next to all of your repos, and then run `./monorepo.sh <source-repo> <destination-repo>`.

```bash
#!/usr/bin/env bash
if [ $# == 0 ]; then
    echo "Usage: $0 src dst"
    echo "* src: The source repository to copy branches from"
    echo "* dst: The destination repository to copy branches to"
fi
specialbranches=(main)
cd "$2" || exit
git remote add "$1" "../$1"
git fetch "$1" --tags
git merge --allow-unrelated-histories "$1/main"
for branch in $(git branch --all | grep '^\s*remotes' | grep "$1"); do
    newbranch="${branch##*/}"
    inarray=$(echo "${specialbranches[@]}" | grep -o "$newbranch" | wc -w)
    if [ "$inarray" -eq 1 ]; then newbranch="$1-$newbranch"; fi
    git branch --track "$newbranch" "$branch"
    git branch --unset-upstream "$newbranch"
done
git remote remove "$1"
```

Finally, if there are any special branches (besides `main`) that you would like migrated over, or branch names that may otherwise have collisions, add it to the `specialbranches` array before running the script.

This will prefix those branch names when copying them over from the source repository; so for our example, lib's `main` branch would be renamed to `lib-main`, once migrated over to the monorepo.
