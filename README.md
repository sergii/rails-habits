# Rails Habits

This guide contain the best approaches and tools for Ruby on Rails 3 & 4 development.

# Table of Contents

* [Unix Shell](#unix-shell)
  * [Keyboard Shortcuts](#keyboard-shortcuts)
  * [Aliases](#aliases)
  * [History Expansion](#history-expansion)
  * [Argument Expansion](#argument-expansion)
  * [SSH](#ssh)
* [Git](#git)
* [Development](#development) (in progress)
* [Testing](#testing) (in progress)
    * [Cucumber](#cucumber) (in progress)
    * [RSpec](#rspec) (in progress)
* [Deployment](#deployment) (in progress)
* [Dev Ops](#devops) (in progress)

# Unix Shell

## Keyboard Shortcuts

__Shortcut__          __Result__

* `ctrl + u`            Deletes the portion of your command __before__  the current cursor position
* `ctrl + w`            Deletes the __word__ preceding the current cursor position
* `ctrl + left arrow`   Moves the cursor to the __left by one word__
* `ctrl + right arrow`  Moves the cursor to the __right by one word__
* `ctrl + a`            Moves the cursor to the __beginning__ of your command
* `ctrl + e`            Moves the cursor to the __end__ of your command

## Aliases

Aliases can be easily created in your `~/.bash_profile` file, and have the following syntax:

```Ruby
alias gb="git branch"
```

Widely used aliases:

```Ruby
# git
alias gs="git status"
alias gb="git branch"
alias ga="git add .; git add -u ."
alias gd="git diff"
alias gdc="git diff --cached"
alias glo='git log --pretty=format:"%h%x09%an%x09%s"'
alias gpro="git pull --rebase origin"
alias editcommit="git commit --amend -m"

# rails
alias r="reset"
alias rs="rails server"
alias rrs="reset && rails server"
alias rc="rails console"

# etc
alias s.="subl ."
alias psgrep="ps aux | grep"
alias h?="history | grep"
alias editbash="open ~/.bash_profile"
or
alias editbash="vim ~/.bash_profile"
```

You make your own, for example my custom aliases:

```Ruby
# folders
alias .="cd ."
alias ..="cd .."
alias cdp = "cd ~/work/Projects/"

# rails
alias armageddon="reset && rake db:drop db:create db:migrate db:seed && rails s"
alias cov='/usr/bin/open -a "/Applications/Google Chrome.app" coverage/index.html'
```

__Note:__ you’ll need to open a new Terminal window for changes in ~/.bash_profile to take place.

## History Expansion

`!!` __- previous command__

```bash
$ rm path/to/thing
Permission denied
$ sudo !!
sudo rm path/to/thing
```

`!$` __- last argument of the previous command__

```bash
$ mkdir path/to/thing
$ cd !$
cd path/to/thing
```

`!string` __- most recent command starting with__

```bash
$ rake db:migrate:reset db:seed
$ rails s
$ !rake # re-runs that first command
```

`!number` __- numbered command__

```bash
$ history | grep heroku
492 heroku run rake search:reindex -r production
495 heroku maintenance:off -r production
496 heroku run rails c -r production
$ !495
```

This technique is perfect for an alias:

```bash
$ alias h?="history | grep"
$ h? heroku
492 heroku run rake search:reindex -r production
495 heroku maintenance:off -r production
496 heroku run rails c -r production
$ !495
```

## Argument Expansion

For commands that take multiple, similar arguments, you can use {old,new} to expand one argument into two or more. For example:

```bash
$ mv app/models/foo.rb app/models/foobar.rb
```

can be

```bash
$ mv app/models/{foo,foobar}.rb
```

or even

```bash
$ mv app/models/foo{,bar}.rb
```

## SSH

### SSH Setup

--

### SSH Configuration

Everyone is familiar with the most basic usage:

```bash
$ ssh user@host.com
```

If the server has only IP address (without DNS name) or a specific port, it becomes difficult to specify these values ​​every time.

```bash
$ ssh administrator@192.168.0.1:3000
```

And now we can use the configuration file. There are two config files:

`~/.ssh/config)` - user's configuration file
`/etc/ssh/ssh_config` - system-wide configuration file

Since I'm one user on my computer, we will use the configuration file `/etc/ssh/ssh_config`. You can use this configuration file to create quick aliases for hosts and setting host-specific options that aren’t addressable by DNS:

```bash
Host staging
  Hostname 192.168.0.1
  Port 3000
  User administrator
```

All you need to do is run `ssh staging` and you’re good to go.

### Invoking Remote Commands with SSH

You can also pass the commands you want to run to the ssh program and use the output just like you would a local operation. For example, if you want to pull down a production database dump, you could:

1. `ssh` into your production server
2. Run `mysqldump` to generate the data dump
3. Run `gzip` to create a compressed file
4. Run `exit` to log out
5. Use `scp` to grab the file off the remote server

Or! You could use this here one-liner:

```bash
ssh user@host.com "mysqldump -u db_user -h db_host -pdb_password db_name | gzip" > production.sql.gz
```

__Bonus tip:__ store long commands like this in [boom](https://github.com/holman/boom) for easy recall.
