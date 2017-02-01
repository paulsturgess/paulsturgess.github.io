---
layout: post
title: "Using Capybara and PhantomJS to test an external website"
date: 2017-02-01 19:14
comments: false
categories:
---
[Capybara](https://github.com/teamcapybara/capybara) is a great way for performing
integration testing of web applications. Typically it is used in development with
the default Rack Test Driver. However, the Rack Test driver does not support JavaScript
and is unable to access HTTP resources outside of your Rack application.

Enter [PhantomJS](http://phantomjs.org/) – a headless implementation of Webkit
that can execute JavaScript, external urls and works with Capybara. The easiest
way to get the two to talk together is via the [Poltergeist gem](https://github.com/teampoltergeist/poltergeist).

I created an example repo with all the required config and setup via Docker: [https://github.com/paulsturgess/capybara_phantomjs](https://github.com/paulsturgess/capybara_phantomjs)

But essentially it boils down to:

```ruby
require 'capybara'
require 'capybara/dsl'
require 'capybara/poltergeist'

$stdout.sync = true

class Demo
  include Capybara::DSL

  def self.run
    new.run
  end

  def initialize
    setup_poltergeist
  end

  def run
    visit('https://google.com')
    save_page
  end

  private

  def setup_poltergeist
    Capybara.register_driver :poltergeist do |app|
      Capybara::Poltergeist::Driver.new(app, polyergeist_options)
    end
    Capybara.default_driver = :poltergeist
  end

  def polyergeist_options
    { js_errors: false }
  end
end

Demo.run
```
