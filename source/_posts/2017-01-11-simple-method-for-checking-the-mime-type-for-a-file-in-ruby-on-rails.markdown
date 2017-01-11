---
layout: post
title: "Simple method for checking a file mime type in Ruby on Rails"
date: 2017-01-11 19:16
comments: false
categories:
---
When accepting file uploads from users of your application, you should always
check the file to ensure malicious content isn't permitted.

If you have a simple Rails `file_field` in a multipart form, you'll get a [ActionDispatch::Http::UploadedFile](http://api.rubyonrails.org/classes/ActionDispatch/Http/UploadedFile.html). This object wraps up the temporary uploaded file and has methods
like `content_type` and `original_filename`.

At first glance it sounds sensible to check the extension (determined from `original_filename`) along with the `content_type`, against a whitelist of allowed
values. However, if the pesky user has renamed the file extension before uploading
the file, your whitelist will not help you as even `content_type` will return the
incorrect value.

This is why checking the mime type of the file is important, but this is not
something Ruby does natively. There are a few Ruby gems around which specifically
handle this problem but the simplest solution I found, without requiring any third
party dependencies, was to use the underlying Operating System and execute the
[file command](http://man7.org/linux/man-pages/man1/file.1.html) via back ticks:

{% highlight ruby %}
  `file --b --mime-type '/path/to/file.txt'`.strip
{% endhighlight %}

This works on both Linux and OS X and will return the mime type such as
'text/plain', which can then be used as part of your validation.
