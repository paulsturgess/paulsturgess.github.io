---
layout: post
title: "Tweeting a screenshot in RubyMotion with UIActivityViewController"
date: 2015-02-05 18:47
comments: false
categories: 
---

The easiest way to allow users to share content from inside your app is to use `UIActivityViewController`.

Use it when you want to allow the user to:

* Send a Tweet
* Share on Facebook
* Save an image to photos
* Send an SMS message / iMessage
* Send an email

In iOS 8 it slides up a panel at the bottom of the screen. The user chooses
how they want to share the content. The options are based on the applications
they have installed. You can also specify which ones to include/exclude.

Here's enough code to create an app with a button that brings up the `UIActivityViewController`, 
takes a screenshot and allows the user to share it along with some text.

Hopefully the code and my comments are self-documenting enough to make sense.

I figured this out through a combination of the Apple docs and this [NSHipster article](http://nshipster.com/uiactivityviewcontroller/).

{% highlight ruby %}
class AppDelegate

  attr_reader :window

  def application(application, didFinishLaunchingWithOptions:launchOptions)
    rootViewController = MyController.alloc.init
    rootViewController.title = 'ActivityViewController Demo'
    rootViewController.view.backgroundColor = UIColor.whiteColor

    navigationController = UINavigationController.alloc.initWithRootViewController(
      rootViewController
    )

    @window = UIWindow.alloc.initWithFrame(UIScreen.mainScreen.bounds)
    @window.rootViewController = navigationController
    @window.makeKeyAndVisible

    true
  end
end

# This subclass is not technically required
# It allows you to switch the text based on the type of service it will be shared on
class ShareText < UIActivityItemProvider

  attr_accessor :text

  # The placeholder text
  def activityViewControllerPlaceholderItem(controller)
    ""
  end

  # The text that will be inserted into the Tweet / Email / SMS etc.
  # You could easily respond to more activity types if you wanted
  def activityViewController(controller, itemForActivityType: activityType)
    if activityType == UIActivityTypePostToTwitter
      "#{text} #{twitter_handle} #{hashtag}"
    else
      "#{text} #{website}"
    end
  end

  def hashtag
    "#yourhashtag"
  end

  def website
    "http://yourwebsite.com"
  end

  def twitter_handle
    "@YourTwitterHandle"
  end

end

# This is a simple controller with one button
class MyController < UIViewController

  def viewDidLoad
    button = UIButton.buttonWithType(UIButtonTypeRoundedRect)
    button.setTitle('Start', forState:UIControlStateNormal)
    button.addTarget(
      self,
      action: 'button_tapped',
      forControlEvents: UIControlEventTouchUpInside
    )
    button.frame = [[100, 260], [view.frame.size.width - 200, 40]]
    view.addSubview(button)
  end

  # This method generates the image of the entire screen
  # Note it will not include the status bar
  def screenshot
    window = UIApplication.sharedApplication.keyWindow
    UIGraphicsBeginImageContextWithOptions(
      window.bounds.size,
      false,
      UIScreen.mainScreen.scale
    )
    window.drawViewHierarchyInRect(
      window.bounds,
      afterScreenUpdates:true
    )
    image = UIGraphicsGetImageFromCurrentImageContext()
    UIGraphicsEndImageContext()
    image
  end

  # We create an instance of our ShareText subclass
  # Pass it into the UIActivityViewController along with the screenshot
  # The UIActivityViewController handles everything else, all you have to do is present the controller.
  def button_tapped
    tc_share_text = ShareText.alloc.init
    tc_share_text.text = "Check out this screengrab!"
    controller = UIActivityViewController.alloc.initWithActivityItems(
      [tc_share_text, screenshot],
      applicationActivities: nil
    )

    controller.excludedActivityTypes = [
      UIActivityTypePrint,
      UIActivityTypeAssignToContact,
      UIActivityTypeAddToReadingList,
      UIActivityTypePostToFlickr,
      UIActivityTypeAirDrop
    ]

    self.presentViewController(
      controller,
      animated:true,
      completion:nil
    )
  end

end
{% endhighlight %}
