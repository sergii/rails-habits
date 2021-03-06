# Rails Habits

This guide contain the best approaches and tools for Ruby on Rails 3 & 4 development.

# Table of Contents

* [Unix Shell](#unix-shell)
  * [Keyboard Shortcuts](#keyboard-shortcuts)
  * [Aliases](#aliases)
  * [History Expansion](#history-expansion)
  * [Argument Expansion](#argument-expansion)
  * [SSH](#ssh)
    * [SSH Setup](#ssh-setup)
    * [SSH Confuguration](#ssh-configuration)
    * [Invoking Remote Commands with SSH](#invoking-remote-commands-with-ssh)
* [Git](#git)
 * [How rolled back to a specific commit](#how-rolled-back-to-a-specific-commit)
* [Development](#development) (in progress)
* [Send Mail](#send-mail)
 * [Overwritte some gems stuff](#overwritte-some-gem-stuff)
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
* `ctrl + k`            Clearing a line from the cursor __onwards__
* `ctrl + e`            Moves the cursor to the __end__ of your command
* `ctrl + r`            for command history __search__

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

__Note 1:__ Also git has it's own aliasing constructs, if you prefer you don't need to set it at the bash level.
__Note 2:__ you’ll need to open a new Terminal window for changes in `~/.bash_profile` to take place or run `source ~/.bash_profile` to take effect.

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

First, we need to check for existing ssh keys on your computer. Open up Terminal and run:

```bash
$ cd ~/.ssh
$ ls
# Lists the files in your .ssh directory
```

If files are missing or do we want to rewrite them to generate a new SSH key, enter the code below. We want the default settings so when asked to enter a file in which to save the key, just press enter.

```bash
$ ssh-keygen -t rsa -C "your_email@example.com"
# Creates a new ssh key, using the provided email as a label
# Generating public/private rsa key pair.
# Enter file in which to save the key (/home/you/.ssh/id_rsa):
...
```

After generate a key, add him to system:

```bash
$ ssh-add id_rsa
```

Now you may copy `id_rsa.pub` key to your server or repo system.

For detailed instructions of setup SSH  (create a key with a name other than the default, use passphrase etc.) please follow the links:
* [Generating SSH Keys](https://help.github.com/articles/generating-ssh-keys) for __github__
* [Set up SSH for Git](https://confluence.atlassian.com/display/BITBUCKET/Set+up+SSH+for+Git) for __bitbucket__

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

* `~/.ssh/config)` - user's configuration file
* `/etc/ssh/ssh_config` - system-wide configuration file

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

# Overwritte some gem stuff

For example overriding Devise controllers and views using namespaces:

Create the following folders:

```bash
app/controllers/overwritten_devise
app/views/overwritten_devise
```

Put all controllers that you want to override into `app/controllers/overwritten_devise` and add `OverwrittenDevise` namespace to controller class names. Registrations example:

```bash
# app/controllers/overwritten_devise/registrations_controller.rb
class OverwrittenDevise::RegistrationsController < Devise::RegistrationsController

  ...

  def create
    # add custom create logic here
  end

  ...    

end 
```

Change your routes accordingly:

```bash
devise_for :users,
           :controllers  => {
             :registrations => 'overwritten_devise/registrations',
             # ...
           }
```

Copy all required views into `app/views/overwritten_devise` from Devise gem folder or use `rails generate devise:views`, delete the views you are not overriding and rename devise folder to `overwritten_devise`.

This way you will have everything neatly organized in two folders.

# Git

## How rolled back to a specific commit
```bash
git checkout -b new_name_branch commit_number
```

for example

```bash
git checkout -b broken_master 3851056
```

or

```bash
git branch broken_master
git push origin broken_master
git reset --hard 3851056
git push origin master --force
```

# Development

# Send Mail

Email sent from a web application is called [transactional email](http://blog.mailchimp.com/what-is-transactional-email/). As a website visitor, you’ve
probably seen transactional email such as these messages:

* sign up confirmation email
* response to a password reset request
* acknowledgment of a purchase
* notice of a change to a user profile setting


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/sergii/rails-habits/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

