---
layout: single
title:  "How to Install SHA-1 Signed Packages on RHEL 9"
date:   2022-08-07 18:00:00 +0900
categories: 
  - Linux
---
On RHEL 9 when installing packages through dnf sometimes you get this error:

{% highlight plaintext %}
[SKIPPED] package-name-1-1.el9.x86_64.rpm: Already downloaded
warning: Signature not supported. Hash algorithm SHA1 not available.
Problem opening package package-name-1-1.el9.x86_64.rpm
The downloaded packages were saved in cache until the next successful transaction.
You can remove cached packages by executing 'dnf clean packages'.
Error: GPG check FAILED
{% endhighlight %}

This is because SHA-1 has been deprecated for safety reasons on RHEL 9. If you are a package maintainer, you should consider switching to a better algorithm such as SHA-256. If you are a user and know what you are doing, you can use the following command to install SHA-1 signed packages:

{% highlight shell %}
update-crypto-policies --set LEGACY
{% endhighlight %}

And don't forget to switch back after installation:

{% highlight shell %}
update-crypto-policies --set DEFAULT
{% endhighlight %}

This error should only happen on RHEL 9 and later. If you are using an older version and want to disable SHA-1 signed packages, you can do this:

{% highlight shell %}
update-crypto-policies --set FUTURE
{% endhighlight %}
