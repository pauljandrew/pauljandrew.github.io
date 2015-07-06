---
layout: post
title: Using Dropbox Chooser with Ionic
---

In our [Ionic](ionicframework.com) app, users have the ability to send messages to each other. With attachments if desired. To support attachments, we use a basic HTML file input.

In Android, this works great. In iOS, however, we're limited by the [app sandbox](https://developer.apple.com/library/prerelease/ios/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html). Out of the box, you can only attach images from the Camera or Photo Roll.

After user queries, we decided to investigate integrating our app with Dropbox. By letting the user attach files from their Dropbox, we provide them with a big workaround for the limitations of iOS.


-----

This turns out to be super easy using [Dropbox Chooser](https://www.dropbox.com/developers/dropins/chooser/js), and specifically with this [awesome plugin for PhoneGap](https://github.com/cv-library/phonegap-plugin-dropbox-chooser).

Once you've [installed and included the plugin](https://github.com/cv-library/phonegap-plugin-dropbox-chooser/blob/master/README.md#prerequisites), you just need to do something like this:

### Create a clickable element:

{% highlight ruby linenos %}
  <div class="item item-icon-left" ng-click="openChooser()">
    <i class="icon ion-social-dropbox"></i>
    Attach with Dropbox
 </div>
{% endhighlight %}

### ...which calls a function:

{% highlight javascript linenos %}
scope.openChooser = function(){
    // Instantiate the DropboxChooser object first.
    var DropboxChooser = window.plugins.DropboxChooser;

    // If this plugin is running on an Android device,
    // the Dropbox app key needs feeding into the class.
    // For iOS, this is not necessary
    if ( ionic.Platform.isAndroid()) {
        DropboxChooser.init('YOUR_DROPBOX_APPID');
    }

    // Call the method 'launchDropboxChooser', which has 3 args:
    // success callback, failure callback and a mandatory
    // flag to specify if you want a 'Preview' link returned
    // ( "true" for a 'Preview' link; "false" for a 'Direct' link).
    DropboxChooser.launchDropboxChooser(dropboxSuccess,function(){},"true");
}

{% endhighlight %}

* **Direct vs Preview link**: a Direct link will **expire** after 4 hours. A Preview link will open in Dropbox's own viewer, but it won't expire. The nice thing about using the Preview link is that, on mobile, if the user *downloading* one of these Dropbox-linked attachments has Dropbox installed on their device, it will open the file using the Dropbox app.

### Your dropboxSuccess callback can look something like this:

{% highlight javascript linenos %}
function dropboxSuccess(results) {
    var selectedFile = results.pop();
    doSomethingWithSelectedFile(selectedFile);
}
{% endhighlight %}

### Finally giving you an object that represents the selected file (with [this format](https://www.dropbox.com/developers/dropins/chooser/js)):

{% highlight javascript linenos %}
selectedFile = {

    // Name of the file.
    name: "filename.txt",

    // URL to access the file, which varies depending on the linkType specified when the
    // Chooser was triggered.
    link: "https://...",

    // Size of the file in bytes.
    bytes: 464,

    // URL to a 64x64px icon for the file based on the file's extension.
    icon: "https://...",

    // A thumbnail URL generated when the user selects images and videos.
    // If the user didn't select an image or video, no thumbnail will be included.
    thumbnailLink: "https://...?bounding_box=75&mode=fit",
 };

{% endhighlight %}

You now have a link to a Dropbox-hosted file, which you can use as you please!