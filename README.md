# /r/teslore

This repository holds the code behind the stylesheet for [/r/teslore](http://reddit.com/r/teslore). This project is primarily a collaboration between @myrrlyn and @numinit, although input and pull requests from the teslore community are welcomed.

## Features

Thanks to programming work provided by numinit, the repository is able to produce a dynamic stylesheet that can be processed and uploaded to reddit automatically.

The banner and date are both time-responsive, and can be programmed to follow standard construction with exceptions for specific events. Banners of different sizes are also supported.

Version information (Git commit info, host machine, and time of compilation) are appended to the icon in the lower right of the footer.

We also offer near-complete coverage of RES elements, including the settings console, menu, etc.

As this project continually aims to reach complete coverage of reddit's DOM while maintaining a modular construction between components, it should (hopefully) be very easy to adapt to individual style needs.

### Getting Started

Clone this repository and copy `config/reddit.yml.example` to `config/reddit.yml`. This file is ignored by Git. Insert the reddit username and password of an account with moderator (stylesheet permissions required) access to your subreddit, and specify the default subreddit under the `subreddit` key. If you wish to hold more than one stylesheet simultaneously, specify the default-upload stylesheet using the `stylesheet` key. There **must** be a `stylesheet.css.sass.erb` file next to a `stylesheet/` directory in the repository root. The triple extension is present because that file is the focal point for three tasks, each of which works with a different language.

### Installation

<small>Note: you must have a working Ruby environment to use this.</small>

If you have not already, run `gem install bundler` to install the package service used here. Also, for Cygwin and Ubuntu users, ensure that you have the `gcc`, `libxml2`, and `libxml2-dev` packages.

Run `bundle install` in this directory to install the required packages. Two programs, AkatoshBot and CraSSius, are hosted on a private server. If you're having trouble accessing them, please write either of us. <small>Note to any Cygwin users: selecting all of the required packages has been an ordeal in the past. The JSON and Nokogiri gems in particular have had dependency issues. If you run into any faults during installation that you can't resolve, I'm very sure I've had the same problem. Write me and copy the failure notice. &mdash;myrrlyn</small>

Once the bundle installation has completed, use `rake` or `bundle exec rake` (if `rake` does not work alone) followed by `reddit:` and the task name and options, if any. The tasks are:

* `compile`: Compiles, but does not transmit, the stylesheet. It can be found in `build/stylesheet.css`.
* `refresh`: Pulls the stylesheet from the subreddit, reprocesses it, and returns the new version. Most useful for scheduled jobs like a calendar.
* `push`:    Compiles and, if successful, pushes the stylesheet to the subreddit. Note: A successful compile does not guarantee a successful push. If the push fails, copy `build/stylesheet.css` to your subreddit's `/about/stylesheet` page and submit. reddit will return the errors there; as yet, we cannot pull specifics via the API.

Options from `config/reddit.yml` can be overridden by including `key=value` parameters in the command. For example, we have our config file targeted at a private testing subreddit, and must explictly call `bundle exec rake reddit:push subreddit=teslore` in order to push to /r/teslore. This helps prevent accidentally uploading a sheet that isn't quite ready to the general public.

## Reading the Style Code

If you're an aspiring CSS author using this repository as an example resource, one of the first things you should notice is that **none of this project is actually written in CSS**. In fact, the only `.css` files in the entire project don't have valid CSS in them; they contain JSON that is parsed by the Ruby tasks before compilation. The only syntactically valid CSS in the project is the comment in the root file, which includes embedded Ruby for flavor.

All of the style documents are written in [Sass](http://sass-lang.com), which allows for a much shorter, cleaner, and more powerful syntax while writing that converts to pure CSS behind the scenes. Sass allows for many features that make writing CSS, especially for a DOM as complex as reddit's, much easier. Note: as long as the current Sass gem supports the deprecated `.sass` syntax, we will continue to use it, as it allows for shorter documents and maintains a similar visual appearance to the Ruby code.

The main differences between Sass and CSS are syntax, nesting capabilities, and functions. For syntax and nesting, plain CSS is written as

```css
parent#id.class child#id.class
{
  property:  value;
  property-subtype:  value;
  property-subtype:  value;
}
```
whereas in Sass the same code could be written as
```sass
parent#id.class
  child#id.class
    property:  value
    property:
      subtype:  value
      subtype:  value
```

This is most useful when used repetitively, so multiple children or property subtypes in a parent or property do not require constantly rewriting the same parent selectors (especially useful in deep or complex nesting).

Sass functions (mixins, extensions, imports, color functions, control structures, etc.) will look familiar to anyone acquainted with the C family of languages and allow for the procedural generation, modification, or reorganization of CSS behind the scenes.

Read the [Sass Reference](http://sass-lang.com/documentation/file.SASS_REFERENCE.html) for information on all the structures used.

### Release Schedule

During the USA academic year, we will try to update the sheet (even if only to cycle the banner) every Saturday. Versions are formatted similarly to the Ubuntu numbering scheme, but with week numbers rather than month numbers. The project is asymptotically approaching a finished state, however, so this may not necessarily be the case.

## Questions, Comments, Feedback, Suggestions&hellip;

myrrlyn is more than happy to answer questions about the Sass code, but has had no part in nor intimate knowledge of the Ruby and barely knows how to work it himself. He's more than happy to answer questions about Sass, CSS, or working with reddit, and can most easily be reached as [/u/myrrlyn](http://reddit.com/u/myrrlyn), assuming he checks his mail. We also welcome comments, feature requests, and release name suggestions (and even pull requests, if you're that dedicated) from the teslore community. Timeliness not guaranteed on anything.
