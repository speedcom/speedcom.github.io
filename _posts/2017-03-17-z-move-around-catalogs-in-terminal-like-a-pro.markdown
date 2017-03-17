---
layout: post
title:  "Z.sh - move around catalogs in terminal like a PRO"
date:   2017-03-17 17:09:17 +0100
categories: [DSP2017, hacks]
---

### Eureka
Have you ever get to known something that is simply brilliant and you feel that you were living without it way too long?

Yes, I've got. I've found `Z.sh` script. Its magic. Its simple. Its fantastic!

### [Z.sh][z.sh]

#### How to install it

1. Person who uses `oh-my-zsh`.
    1. You probably have already downloaded and having placed under `~/.oh-my-zsh/plugins/` path.
    2. In order to enabled it open `.zshrc` file, find `plugins` section and add name of the plugin (which is `z`, hah). In my config this looks like: `plugins=(git, git-extras, z)`

2. Others (copy-pasted from README)
```
Installation:
       Put something like this in your $HOME/.bashrc or $HOME/.zshrc:
              . /path/to/z.sh
```
Now try to cd around for a while to build up the db. You will be amazed by simplicity and usability of it.

Cheers

[oh-my-zsh]: https://github.com/robbyrussell/oh-my-zsh
[z.sh]: https://github.com/rupa/z
