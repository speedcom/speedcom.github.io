---
layout: post
title:  "Z.sh - move around catalogs in terminal like a PRO"
date:   2017-03-17 17:09:17 +0100
categories: [DSP2017, hacks]
---

### Eureka
Have you ever get to known something that is simply brilliant and you feel that you were living without it way too long?

Yes, I've got. I've found [`Z.sh`][z.sh] script. Its magic. Its simple. Its fantastic!

#### Whats it is
Its the script that tracks your most used directories, based on 'frecency'. From README we can read that
"After  a  short  learning  phase, z will take you to the most 'frecent' directory that matches ALL of the regexes given on the command line, in order."

I was used to spend a lot of time to find appropriate catalog in terminal. To make it simpler I was adding more and more path aliases to shell config. That sounds like sth that could be improved.

How `z.sh` help here? To make it easier to visualize, as an example, lets take `Dropbox` catalog into account. Its placed under `/Users/myname/documents/Dropbox` path on my computer.
Now, when `z.sh` has already registered Dropbox catalog's path I am not forced to think about it anymore (you need to cd there at least once because z.sh does not scan your disk). I'm typing simple `z dropbox` command and... I'm getting moved there correctly. I can even use shorter version like `z drop` and it still works (`z.sh` script uses regexp for finding matches). If `z.sh` didn't find any matches it simply leave you in current directory. While typing your command you can use tabulator which will unfold your regexp into path that script is thinking you want to move.


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

Cheers and don't thank me

[oh-my-zsh]: https://github.com/robbyrussell/oh-my-zsh
[z.sh]: https://github.com/rupa/z
