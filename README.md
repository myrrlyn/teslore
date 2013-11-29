# /r/teslore

This repository holds the current state of /r/teslore CSS work. It includes scripting support to automatically upload CSS to Reddit.

## Getting started

Clone this repository and copy `config/reddit.yml.example` to `config/reddit.yml`. This file is ignored by Git. Fill in your Reddit username and password. The default subreddit to upload to is specified by the `subreddit` key. The default stylesheet to upload is specified by the `stylesheet` key. There *must* be a `stylesheet.css` file next to a `stylesheet` directory in the root of this repository.

## Installing stuff

Run `bundle install` in this directory to install the required packages. You might need the `gcc`, `libxml2`, and `libxml2-dev` packages in addition to `ruby` on cygwin or Ubuntu. If `bundle` wasn't found, try `gem install bundler` first.

## Rake tasks

To pull down, process, and reupload a stylesheet, use `rake reddit:refresh`.

To compile and upload the CSS, use `rake reddit:push`.

To compile the CSS without uploading it, use `rake reddit:compile`. Output will be saved to the `build` directory.

If you want to override the default subreddit or stylesheet, try something like `rake reddit:refresh subreddit=teslore`.
