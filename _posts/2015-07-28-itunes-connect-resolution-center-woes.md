---
layout: post
title: A (Not So) Easy Mistake With iTunes Connect & Metadata Rejections
comments: true
---

*What follows is my contribution to the storied genre of App Review horror stories. App names and version numbers have been changed to protect the innocent.*

-------
![iTunes Connect](/public/itunes_connect.png "Doing what it does best: going haywire.")

Resolution Center is the part of iTunes Connect where the Apple reviewers communicate with you to request extra or corrected metadata info while your app goes through review.

A few weeks ago, I received a message explaining they couldn't log in with the test user I had provided. Sure enough, I had given the wrong password. However, while waiting the [7-8 days](http://appreviewtimes.com/) for this update (Let's call it <span style="color:red">1.2</span>) to be reviewed, I had found a serious bug in it that meant I wouldn't want to release it anyway.

I had read [this blog post](http://www.brynbodayle.com/an-easy-mistake-with-itunes-connect-metadata-rejections/) by Bryn Bodayle which, in a nutshell, explains that you should not re-submit your app after a *Metadata Rejection*. Instead, reply back to Apple saying that the Metadata has been updated, and it will go straight to *In Review* rather than back to the start of the weeklong queue.

Spying an opportunity to jump the queue with my new bug-free version, I then:

  1. Updated the number of the version under review in the "Versions" tab from <span style="color:red">1.2</span> to <span style="color:blue">1.3</span>.
  2. Removed the previously attached build and attached my bug-free new IPA.
  3. Updated the Metadata info with the correct test user password.
  4. Replied back to Apple via the Resolution Center that the Metadata was now correct.
  5. Waited....

Sure enough, after a few hours my app went straight to *In Review*, was approved and moved to *Pending Developer Release*.

I checked everything out, and everything confirmed that <span style="color:blue">1.3</span> would be released. The "Build Details" page showed the Bundle Version String as being <span style="color:blue">1.3</span>. No mention of <span style="color:red">1.2</span> anywhere. I had managed to avoid having to go through the waiting process a second time. All good.

I released the app, only to discover to my dismay that, despite the app being listed in the App Store as <span style="color:blue">1.3</span>, the actual product that was downloaded to user devices was version...... <span style="color:red">1.2</span>. Which meant we now had a bunch of bugs in production.

(We have a page in the app that lists the version number from config.xml/[appname]-Info.plist so I was able to confirm exactly why my bug was re-appearing. Glad I put that in there and didn't leave it up to Apple to display the version number accurately.)

Once I figured out what had happened, I began the review process for a new version <span style="color:green">1.4</span> and requested an expedited review time, giving an explanation of how the incorrect IPA had become released.

-----
<table class="image">
<caption align="bottom">Early prototype of iTunes Connect</caption>
<tr><td align="center"><img src="/public/HAL9000.svg" alt="Credit: Cryteria"/></td></tr>
</table>
**From bad to worse...**

After a 2 day wait, version <span style="color:green">1.4</span> went to *In Review* and then *Pending Developer Release*. I received no response from Apple regarding the expedited review process but the short turnaround *suggests* that they did expedite it. I submitted <span style="color:green">1.4</span> to the App Store and it went to "Ready for Sale" on a Friday afternoon, with the usual proviso that it could take up to 24 hours to actually reach the App Store.

On Monday (72+ hours later), I received an email explaining that a problem had been found during App Review: **Invalid or Non-Increasing CFBundleShortVersionString**.

That certainly seemed odd given that 1) there was nothing wrong with the version number (<span style="color:green">1.4</span>) and 2) the App Review was already over!

The app's status in iTunes Connect remained that <span style="color:green">1.4</span> was **Ready for Sale**, but when checking in the App Store, <span style="color:blue">1.3</span> was still the available version.

So things were getting pretty crazy at this point. Review rejections when the review was already finished, incorrect version displayed in the App Store and yet another version actually installed to devices when the app was downloaded.

I rustled up another version of the app with nothing changed except an incremented version number of 1.5. I submitted it for review and sent my second request for Expedited Review of the week. To Apple's credit, they responded to this one within an hour and actually sent an (automated) email back.

The app then stayed *In Review* for well over a day, before being approved. Around the same time they approved 1.5, <span style="color:green">1.4</span> finally became available in the App Store, bringing the whole sorry saga to a happy end.

I suspect someone at Apple was doing some tinkering behind the scenes during the review.

-----

**tl;dr**: If you get a *Metadata Rejection*, [don't](http://www.brynbodayle.com/an-easy-mistake-with-itunes-connect-metadata-rejections/) click "Submit for Review" and DON'T update the version you have under review. It will not correctly update and the IPA file you originally submitted at the start of the process will be released instead, leading you into a downward Kafkaesque spiral with Apple's famously opaque App Review.

{% include twitter_plug.html %}