---
layout: post
title: "How to setup TestFlight for a RubyMotion app"
date: 2013-05-23 11:06
comments: false
categories:
---

The official RubyMotion guide for [installing TestFlight](http://www.rubymotion.com/developer-center/articles/testflight/)
got me most of the way there, but there were a few additional steps I needed to
get everything working.

First and foremost [I got my app running on my own device via usb](http://paulsturgess.co.uk/blog/2013/05/10/using-rubymotion-to-provision-an-app-onto-your-iphone/).

However, for TestFlight, an Adhoc Provisioning Profile is required, as opposed
to a Development Provisioning Profile. Essentially this is an Apple approved
license to distribute your app to a limited number of devices without using the
App Store.

## Create a distribution certificate

Go to the Apple iOS developer website and create a Distribution certficate in a
similar way to how you created your Developer certificate.

## Create a fixed App ID

This is optional, but it felt right to me to use a fixed App ID instead of a
wildcard.

## Create an adhoc distribution profile

Finally, still on the Apple iOS Developer website, create an Adhoc distribution
profile, download it and double click it to install it.

## Setting up your Rakefile for multiple environments

I wanted to use both my Developer and Adhoc Provisioning Profiles at the same
time. Without having to worry about switching them when required.

My Rakefile looks like this:

    Motion::Project::App.setup do |app|
      env = if ENV['adhoc'] == '1'
              'adhoc'
            else
              'dev'
            end

      app.name = 'MyApp'

      # Provisioning profiles
      if env == 'adhoc'
        app.identifier = 'com.mydomain.myapp'
        app.codesign_certificate = 'iPhone Distribution: Company (XXXXXXXXXX)'
        app.provisioning_profile = '/path/to/adhoc/provisioing/profile/example.mobileprovision'
        app.entitlements['get-task-allow'] = false
      else
        app.identifier = 'com.mydomain.myapp'
        app.codesign_certificate = 'iPhone Developer: My Name (XXXXXXXXXX)'
        app.provisioning_profile = '/path/to/development/provisioing/profile/example.mobileprovision'
      end

      # TestFlight config
      app.testflight.sdk = 'vendor/TestFlight'
      app.testflight.api_token = 'sometoken'
      app.testflight.team_token = 'anothertoken'
    end

    desc "Set the env to 'adhoc'"
    task :set_adhoc do
      ENV['adhoc'] = '1'
    end

    desc "Run Testflight with the adhoc provisioning profile"
    # e.g. rake tf notes="My release notes"
    task :tf => [
      :set_adhoc,
      :testflight
    ]

Credit to Hwee-Boon Yar for writing up [how to setup the Rakefile for multiple environments](http://hboon.com/different-settings-for-development-and-adhoc-builds-in-rubymotion/)

Your API token is under your account settings on TestFlight at: [https://testflightapp.com/account/#api](https://testflightapp.com/account/#api).

You can find your team API token at the following url: [https://testflightapp.com/dashboard/team/edit/](https://testflightapp.com/dashboard/team/edit/)

One key bit of information I was initially missing was setting `get-task-allow`
to false. This is only required for the Adhoc Provisioning Profile.

## Use a custom rake task to deploy to TestFlight

Notice the rake task at the bottom sets the adhoc variable to ensure the Adhoc
provisioning profile is used before firing off the TestFlight task added by
`motion-testflight`.

So from now on I release to TestFlight via:

    $ rake tf notes="My release notes"