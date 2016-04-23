---
layout: post
title:  "Bootstrap 4 is coming and it's in Sass!"
date:   2016-04-23 21:22:49
comments: true
author: Přemysl Donát
---
Did you know that Twitter Bootstrap 4 will be in Sass and not LESS anymore? That means we can directly use it inside Rails projects with official [bootstrap](https://rubygems.org/gems/bootstrap) gem.

No need to use [bootstrap-sass](https://github.com/twbs/bootstrap-sass) or even LESS version [twitter-bootstrap-rails](https://github.com/seyhunak/twitter-bootstrap-rails).

# How to integrate into Rails

It's super easy. Barely needs a post but anyways:

Gemfile:

~~~
gem 'bootstrap', '>=4.0.0.alpha3'
~~~

application.css.scss:

~~~
/*
 * This is a manifest file that'll be compiled into application.css, which will include all the files
 * listed below.
 *
 * Any CSS and SCSS file within this directory, lib/assets/stylesheets, vendor/assets/stylesheets,
 * or vendor/assets/stylesheets of plugins, if any, can be referenced here using a relative path.
 *
 * You're free to add application-wide styles to this file and they'll appear at the top of the
 * compiled file, but it's generally better to create a new file per style scope.
 *
 */

@import "variables";
@import "bootstrap";
@import "my_styles";
~~~

Note that we don't use Sprockets requires anymore, just normal Sass imports. But practically it's similar. `variables.css.scss` and `my_styles.css.scss` are just another files in same folder as `application.css.scss`.

And that's really all! :) Now head to the [Bootstrap 4 docs](http://v4-alpha.getbootstrap.com/) to see what other things are new in Bootstrap.
