---
layout: post
title: "Using RubyMotion to provision an app onto your iPhone"
date: 2013-05-10 21:49
comments: false
categories:
---

So you've got your [Apple developer account](https://developer.apple.com/) and
now you want to get your RubyMotion app onto your iPhone.

At the end of these instructions you will be able to deploy multiple apps using
the same provisioning profile. Ideal for prototyping.

## Summary of what you need to do

- Generate an iOS certificate
- Create an App ID
- Register your iPhone with your Apple Developer account
- Create a Provisioning Profile
- Update your RubyMotion Rakefile to use the Certificate and the Provisioning Profile

## Generate an iOS certificate

Open the Keychain Access program on your Mac and from the dropdown menu choose:

`Keychain Access > Certificate Assistant > Request a certificate from a certificate authority`

The 'common name' is the name for your private key. You can choose whatever
you like.

Just make sure you select to `save to disk`.

Now you need to sync this up with your Apple Developer account.

Visit [The Apple iOS Dev Centre](https://developer.apple.com) and go to the
`Certificates, Identities & Profiles` section.

Go to `Certificates > Development` and select to add a new certificate. Follow
the step process, uploading the certificate you just created.

At the end you need to download the `.cer` file that gets generated. Double
click it to add to your Keychain.

## Create an App ID

In that same section of the website you can create an App ID by going to
`Identifiers > App ID's`.

I chose a name of `exampleapp` and opted for the Wildcard App ID. The wildcard
ID means I don't need to create a new App ID & Profile for each new app I want
to try out on my iPhone.

Obviously when it comes to App Store submission, then it will make sense to have
a dedicated App ID. Also Apple recommend a dedicated App ID if you're testing
advanced features like Push Notifications.

## Register your iPhone with your Apple Developer account

Again staying on the iOS Dev Center website, you need to register all the
devices you want to deploy your app onto.

You only need to do this once for each device, but you'll need to know the UDID
for each one.

I decided to install a handy free app (simply called UDID) that emails you this
reference and a host of other info straight from your iPhone. There are loads
of free apps that do similar things on the App Store.

## Create a Provisioning Profile

The last task on the iOS Dev Center website is to create a
`Development Provisioning Profile`.

All the instructions are self-explanatory so I won't repeat them here.

Just make sure you download and double click the `.mobileprovision` file at the end.

## Update your RubyMotion Rakefile to use the Certificate & Provisioning Profile

Update your `Rakefile` in your app:

    Motion::Project::App.setup do |app|
      app.name = 'ExampleApp'
      app.identifier = 'com.yourdomain.exampleapp'
      app.codesign_certificate = 'iPhone Developer: Your Name (XXXXXXXXXX)'
      app.provisioning_profile = '/path/to/your/provisioning/profile/Example_App.mobileprovision'
    end

Note that Wildcard App Id means you can use any name for the third part of the
identifier.

Also for the `codesign_certificate`, you can view this in the Keychain Access
program.

Finally deploy to the device via:

    $ rake device