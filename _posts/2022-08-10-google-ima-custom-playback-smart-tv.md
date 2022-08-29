---
layout: post
title: Google IMA Custom Playback on Smart TV
comments: true
---

While integrating Google pre-roll ads into a number of different Smart TV players recently, I came upon some slightly confusing behavior that may be worth documenting in the hope of saving someone else the debugging time.

At first glance, [the setup steps for the HTML5 SDK](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side) are well-documented and everything was easily integrated into our HTML5 video player.

Once we started to test the ads on a variety of different platforms though, we started to see some platforms where it worked perfectly and others where the ads didn't display - **PS4** and **Opera** devices, specifically.

-----

It turns out, there are **two** ways the IMA ad can display:


1. It will display a div that covers up your video player and show the ad within a separate video player (Standard playback)

2. It will use your video element (Custom playback)

## Custom Playback

While there are references to custom playback throughout the [API reference](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/reference/js/google.ima.AdsManager), and [older blog posts](https://ads-developers.googleblog.com/2015/06/ima-html5-rendering.html), it seems to have slightly disappeared from the current docs, and is not explicitly called out as something to watch out for.


This is probably because it mostly works flawlessly on more common platforms. The IMA SDK checks the userAgent string of the device it's running on, and makes its decision whether to use custom playback based on an internal list of devices. This is why it becomes an issue when you're running on 10+ niche Smart TV platforms, all of which may not be accounted for in the SDK's logic. 

The SDK also doesn't output anything to its logs when deciding which of the playback modes to use. As such, it took me a while to figure out what was happening when I first encountered this on the PS4 - after inspecting the behavior of the ad video on different platforms, I noticed the differing implementations.

I also found a message from Google that says Smart TV devices are [not officially supported for the HTML5 SDK](https://groups.google.com/g/ima-sdk/c/ekChugmqT3E) - "not officially supported" is par for the course for Smart TV development though -  and I can confirm that it works well on a wide variety of platforms.  


## The solution


{% highlight javascript linenos %}
 this.adDisplayContainer = 
 new google.ima.AdDisplayContainer(this.adsContainer, this.needsCustomPlaybackForGoogleIMA() ? this.player : undefined);
{% endhighlight %}

{% highlight javascript linenos %}
if (this.adsManager.isCustomPlaybackUsed()) {
  this.removePlayerEventListeners();
}
  {% endhighlight %}

In our case, as we wanted to control the behaviour ourselves, rather than letting the SDK decide for us, we only passed in our player object to the [AdDisplayContainer](https://developers.google.com/interactive-media-ads/docs/sdks/html5/client-side/reference/js/google.ima.AdDisplayContainer) when we definitely needed Custom Playback. 

We also needed to add extra logic in some other places to accommodate the different types of playback - if custom playback is used, you will want to remove any of your own event listeners from the video player, so they don't trigger while the ad video is playing.

Hope this helps!

{% include twitter_plug.html %}