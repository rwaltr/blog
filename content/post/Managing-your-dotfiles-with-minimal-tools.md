---
title: Managing Your Dotfiles With Minimal Tools 
subtitle: A Sysadmins Take On Config Managment 
date: 2019-10-13
author: Ryan Walter
tags: ["git", "stow","dotfiles","linux"]
---

Some of us like to keep clean directories. I bet if I asked you to run `ls` It would be a short list.

```bash
Desktop
Documents
Downloads
Firefox_wallpaper.png
Games
Landscape-pictures-28.jpg
Library
localLibrary
Music
Pictures
Public
src
Sync
Templates
Videos
```



Now... run `ls -a ~`. What do you see?

Depending on which OS you use. Probably a lot more files than you knew existed. Let me show you mine. 

```bash
.
..
.ansible
.ansible_galaxy
.aqbanking
.aria2
.atlauncher
.bash_history
.bash_logout
.bash_profile
.bashrc
.bin
.cache
.ccache
.config
.dbus
Desktop
Documents
.dotfiles
Downloads
.esd_auth
.factorio
Firefox_wallpaper.png
.fltk
Games
.gitconfig
.gnupg
.hledger.journal
.hledger-web_client_session_key.aes
.ICEauthority
.irssi
.java
.joplin
.kpcli-history
Landscape-pictures-28.jpg
.lesshst
Library
.local
localLibrary
.minecraft
.mozilla
.mputils
Music
.mysql_history
.ntfy.yml
.nv
.oracle_jre_usage
.ovirtshellhistory
.ovirtshellhistory.tmp
.ovirtshellrc
Pictures
.pki
Public
.pylint.d
.python_history
.spicec
src
.ssh
.steam
.steampath
.steampid
Sync
.task
.taskrc
.technic
.temp
Templates
.texlive2018
.timewarrior
.Trash-1000
.uml
Videos
.viminfo
.vnc
.volo_airsport
.vscode
.vscode-oss
.vs-kubernetes
.wget-hsts
.wine
.x2go
.x2goclient
.Xauthority
.xsession-x2go-fsteel.waltr.lab-errors
.yadm
```

*Whoa....* Where did all those come from?

These are known as "Dotfiles" They are files that contain information based on their names and the programs that use them. When you perpend a filename with a dot `.` it will be hidden from plain view. Similar if you did the same with a `$` in Windows.

I've currently been on a crusade to manage my dotfiles, I want to have my dotfiles versioned, easy to install, and require minimal amount of special tools. My reasoning is the following...

- Versioning means I can roll back my changes if I bork a config and do not know where exactly why I have made a change. (`git blame` is a good tool for that if you practice verbose commits)

- Easy to install means these to me.
  - I can easily select which tools I want to install.
  - I can do so in as few lines as possible
  - I do not require any special tools.

This leads us to some special tools that I have chosen. Git and Stow. But why? There are many dotfile managers out. 

1. Git and Stow are common tools used out in the wild
  - `git` and `stow` are either older or extremely popular programs, It would be hard to find a system that did not include it in their package managers. (Even Alpine, a distribution known for its small footprint, has `git` and `stow` in its repos.

1. Git are Stow are easy to use programs. 
  -  Git and stow require minimal configuration to get started. Git can clone repos without credentials or setup (Unless you use a private repo). Stow doesn't use a configuration file, but the directory tree of the folders underneath it to determine what to do.

1. Git allows for git within git. Known as `git submodules`
  - This was originally for projects that would use other repositories within their own projects. It allows you to keep a directory within your git repo to actually represent a different repo all together, On the working dir they look identical, but in Git they are represented by different types of pointers.
  - You may want to keep different aspects of your dotfiles separate from your main repo. Or use someone elses Dotfiles as a reference (Such as themes for i3, Awesome, etc).

## So, You decided to Git your dotfiles... What next?

**Read your manpages** I'm not joking. For everything you want to dotfile, check your manpages. Why? Programs will often check 3 locations for configs. 

- A system location.
- A .config location.
- Your home dir. 

For example. Lets reference i3's Manpage

```man
When starting, i3 looks for configuration files in the following order:
    1. ~/.config/i3/config (or $XDG_CONFIG_HOME/i3/config if set) 
    2. /etc/xdg/i3/config (or $XDG_CONFIG_DIRS/i3/config if set) 
    3. ~/.i3/config 
    4. /etc/i3/config 
You can specify a custom path using the -c option. 
```

That's right, i3 has 4 locations it checks by default without options. 3 of them in your home directory, one of them being a system shared directory.

So... since we are version managing and symlinking all our dotfiles anyways, We can have some fun with putting our files in locations that will not clutter our base homedir, yet findable without special configuration.

## How to use `Stow`
##### Stow it away!

Stow original usecase is long gone. It has been replaced by package managers. How it use to manage those issues is what will benefit us.

What stow does, is take a folder from the working dir. Then apply to the directory above it a collection of syslinks matching the pattern of the folder below it.

Now thats a garbage description. Lets see the manpage.

```
stow - manage farms of symbolic links
```

**That's even worse**

Let's do an example

This is my test dir before running stow. 

```

/home/blackphidora/notmyhome/
└── stowsdir
    ├── bash
    │   └── .bash_profile
    ├── i3
    │   └── .config
    │       └── i3
    └── zfs
        └── .config
            └── somezfs

8 directories, 1 file

```
Lets try doing `cd stowsdir && stow *`.
```
/home/blackphidora/notmyhome/
├── .bash_profile -> stowsdir/bash/.bash_profile
├── .config
│   ├── i3 -> ../stowsdir/i3/.config/i3
│   └── somezfs -> ../stowsdir/zfs/.config/somezfs
└── stowsdir
    ├── bash
    │   └── .bash_profile
    ├── i3
    │   └── .config
    │       └── i3
    └── zfs
        └── .config
            └── somezfs

11 directories, 2 files
```

Did you catch on yet? Yeah, It creates syslinks from the top down based on the folder stow references. 

We can use this separate concerns for different applications. Such as Bash an i3.

## Lets smash Git into it. 
##### Git it?

Now that we have all of our files in a single folder. We can now manage it with git easily.

`git init stowsdir`

I won't go to much into git, There are better git tutorials out there than what I have. If you haven't used git, Learn the following.

- `git commit`
- `git clone`
- `git submodule`
- `git commit`
- `git add`

Once you have your dotfiles in a repository. All you need to do is run the following, 

```bash
sudo dnf install git stow -y && git clone https://<yourgitrepo> .dotfiles && cd dotfiles && stow *
```

You may need to remove the original .bashrc in your home dir.



## Git Submodules
##### Yo Dawg.

I use git submodules to include a refrence to [liquidprompt's repo](https://github.com/nojhan/liquidprompt) in my dotfiles, This is the exception to my quote "Stow's old use case is long gone".

```bash

/home/blackphidora/.dotfiles/liquidprompt
└── .config
    ├── liquidprompt
    │   ├── liquidpromptrc
    │   ├── liquid.ps1
    │   └── liquid.theme
    └── liquidpromptrc -> liquidprompt/liquidpromptrc
/home/blackphidora/.dotfiles/liquidpromptprogram
└── .bin
    └── liquidprompt
        ├── CHANGES
        ├── contrib
        │   ├── dist
        │   │   ├── archlinux-aur
        │   │   │   ├── liquidprompt.install
        │   │   │   └── PKGBUILD
        │   │   └── debian
        │   │       ├── create_deb.sh
        │   │       └── liquidprompt
        │   │           └── DEBIAN
        │   │               └── control
        │   └── README
        ├── CONTRIBUTING.md
        ├── demo.png
        ├── example.bashrc
        ├── .git
        ├── .github
        │   └── ISSUE_TEMPLATE.md
        ├── LICENSE
        ├── liquidprompt
        ├── liquidprompt.plugin.zsh
        ├── liquidpromptrc-dist
        ├── liquid.ps1
        ├── liquid.theme
        ├── .mailmap
        ├── pmset-simulator
        ├── README.md
        └── test.sh

11 directories, 25 files
```

If you would like to add a submodule to your repo. Use `git submodule add <your repo> <file to place under>`


Have any questions? Feel free to reach out me be using the social links below.
