---
title: ~/.gitconfig
layout: knowledge
tags: [git]
---

Useful global git settings in `~/.gitconfig` file.

```
[fetch]
	prune = true

[core]
	excludesfile = ~/.gitignore
	pager = less -FXR

[init]
	defaultBranch = main

[pager]
	branch = false
```
