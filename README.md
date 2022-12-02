# MockLab Website

This is the source project for [https://www.mocklab.io](https://www.mocklab.io).
It's built with [Jekyll](https://jekyllrb.com/) 3.x and is based on the [Minimal Mistakes](https://mmistakes.github.io/minimal-mistakes/) Jekyll theme.

## Setting up your dev workspace

### Ruby

Jekyll runs on Ruby, so it's advisable to install a ruby version manager such as [rbenv](https://github.com/rbenv/rbenv).

Version 2.7.0 of ruby is known to work with this version of Jekyll, so install this version
via your version manager:

```
rbenv install 2.7.0
```

Next install the Bundler and Jekyll ruby gems:

```
rbenv exec gem install jekyll bundler
```

### Node

Some bits of the build e.g. web asset processing are done using Node and NPM. As with
Ruby it's a good idea to install a version manager, in this instance [nvm](https://github.com/nvm-sh/nvm).

Use nvm to install and activate Node v8.11.2:

```
nvm install 8.11.2
nvm use 8.11.2
```

## Running locally

Run the following script:

```
./serve.sh
```

The site will be served on [http://localhost:4000/](http://localhost:4000/).
