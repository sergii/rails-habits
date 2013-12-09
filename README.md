# Rails Habits

This guide contain the best approaches and tools for Ruby on Rails 3 & 4 development.

# Table of Contents

* [Unix Shell](#unix-shell)
  * [Keyboard Shortcuts](#keyboard-shortcuts)
  * [Aliases](#aliases)
  * [History Expansion](#history-expansion)
  * [Argument Expansion](#argument-expansion)
* [Git](#git)
* [Development](#development)
* [Testing](#testing)
    * [Cucumber](#cucumber)
    * [RSpec](#rspec)
* [Deployment](#deployment)
* [Dev Ops](#devops)

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
  ```

You make your own, for example my custom aliases:

  ```Ruby
  # folders
  alias .="cd ."
  alias ..="cd .."
  alias cdp = "cd ~/work/Projects/"

  # rails
  alias armagedon="reset && rake db:drop db:create db:migrate db:seed && rails s"
  alias cov='/usr/bin/open -a "/Applications/Google Chrome.app" coverage/index.html'
  ```

__Note:__ you’ll need to open a new Terminal window for changes in ~/.bash_profile to take place.

## History Expansion

`!!` __- previous command__

  ```Ruby
  $ rm path/to/thing
  Permission denied
  $ sudo !!
  sudo rm path/to/thing
  ```

`!$` __- last argument of the previous command__

  ```Ruby
  $ mkdir path/to/thing
  $ cd !$
  cd path/to/thing
  ```

`!string` __- most recent command starting with__

  ```Ruby
  $ rake db:migrate:reset db:seed
  $ rails s
  $ !rake # re-runs that first command
  ```

`!number` __- numbered command__

  ```Ruby
  $ history | grep heroku
  492 heroku run rake search:reindex -r production
  495 heroku maintenance:off -r production
  496 heroku run rails c -r production
  $ !495
  ```

This technique is perfect for an alias:

```Ruby
$ alias h?="history | grep"
$ h? heroku
492 heroku run rake search:reindex -r production
495 heroku maintenance:off -r production
496 heroku run rails c -r production
$ !495
```

## Argument Expansion

For commands that take multiple, similar arguments, you can use {old,new} to expand one argument into two or more. For example:

  ```Ruby
  mv app/models/foo.rb app/models/foobar.rb
  ```

can be

  ```Ruby
  mv app/models/{foo,foobar}.rb
  ```

or even

  ```Ruby
  mv app/models/foo{,bar}.rb
  ```
