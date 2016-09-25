---
layout: post
title: "Building a Chrome Extension in ES6 FOR KIDS!"
date: 2016-09-23
excerpt: "Utilizing ES6 and the Chrome API to build an extension in remembrance of Harambe."
tags: [tutorial, code, for kids, javascript, es2015, es6, chrome extension, harambe]
feature: assets/img/post_images/2016-09-23-harambe-feature-photo.jpg
comments: true
---

## What we will be building:
In the current digital age, we are constantly bombarded with all different types of media telling us how we should look, how we should act, and how we should feel. We tend to forget about the things that matter, so today we will be building a Chrome extension that will always remind us to not take our lives for granted. InMemoriam is a Chrome extension that will replace all images on every website you visit with a wonderful photo of Harambe.


<figure>
	<img src="/assets/img/post_images/2016-09-23-harambe.png">
	<figcaption><a href="/assets/img/post_images/2016-09-23-harambe.png" title="InMemoriam in action">InMemoriam in action</a></figcaption>
</figure>

## What we need to get started:
* A computer
* Google Chrome
* Text Editor (such as [Vim](http://www.vim.org/) or [Sublime](https://www.sublimetext.com/))
* Basic knowledge of what the DOM is, what variables are, what arrays are, and what loops are.

## Some background info:
To get started building our Chrome extension, we should first briefly understand some of the files we will be creating.

While a Chrome extension can be quite elaborate, we will start our extension off by just creating two files.
First make a folder named "InMemoriam" and create these two files. 

* `manifest.json`
* `content.js`

Then download this [image](http://www.gannett-cdn.com/-mm-/748c68a68596b4b3be54484af8d31875e45f9fe5/c=179-0-2659-3307&r=537&c=0-0-534-712/local/-/media/2016/05/29/Cincinnati/Cincinnati/636001135964333349-Harambe2.jpg) and save it as `harambe.jpg` in the same folder.

The `manifest.json` is our main configuration file, it tells Chrome important information about our extension such as what files are needed, what permissions the extension should be able to have, and what icons the extension should have.

Content scripts have access to everything that we see when we view a web page (aka the [DOM](http://www.w3schools.com/js/js_htmldom.asp)). Our `content.js` is a content script which doesn't technically have to be named "content.js" since we define what our content scripts are in the `manifest.json` file, but having this name is more descriptive. This is useful because our content script will be able to access and change all the images on our web pages to Harambe!

## Building the extension

~~~ json
{
  "manifest_version": 2,
  "name": "InMemoriam",
  "description": "This extension will convert all images to Harambe",
  "version": "1.0",
  "content_scripts": [
    {
      "matches": [
        "http://*/*",
        "https://*/*"
      ],
      "js": [
        "content.js"
      ],
      "run_at": "document_end"
    }
  ],
  "web_accessible_resources": ["harambe.jpg"]
}
~~~

Our `"manifest_version"` tells Chrome what version of configurations we are using in our extension. Currently, the newest version is 2, so we will be using that.

The `"name"` key defines what the name of the extension should be and the description is a brief summary of what the extension can do. 

For `"version"` number, I've thrown in an arbitrary number, all it does is specify what version the extension is, I could put anything I want here essentially.

In the `"content_scripts"` key is where it gets a little more interesting. Content scripts run on every tab you have open, but they can only run on web pages that you give permission on. If we were to place only http://google.com in the matches array, the content script would only run if we were on http://google.com. Here, we use http://\*/\* and https://\*/\* because we want the content script to run on every web page. The "*" is a wild card character that matches anything.

We give the `"js"` key a value of an array with `"content.js"` in it to define our content script as "content.js" so the extension knows where to look for the code.

We give `"run_at"` a value of `"document_end"` because we want this content script to run only AFTER the entire web page has loaded.

Finally, we define a `"web_accessible_resources"` key with the value of `["harambe.jpg"]` because we want our extension to be able to access the Harambe picture we stored in the extension folder.

Now, lets put some code in `content.js`:

~~~ javascript
(function(){
  const images = document.getElementsByTagName('img')
  const harambePic = chrome.extension.getURL("./harambe.jpg")
  Array.from(images).forEach(img => img.src = harambePic )
})()
~~~

First, we get all the images on the page and store it in a constant variable called images. Then we use the chrome.extension API to get the url of our Harambe picture from within the extension and store it in a variable named harambePic. 

`const` is a ES6 keyword similar to `var` in JavaScript, however, when you declare a variable using the keyword 
`const` it can not be reassigned again and it also has block level scoping. In the scope of this project, it doesn't actually matter if you use `var`, `let`, or `const`, but I like to use it just because it prevents me from goofing and accidently overwriting a variable.

When we use `console.log(document.getElementsByTagName('tag'))`, we get something back like this:
`[ <img src='http:imgsrc.com'>, ... ]`. You might think that this is an array, but it's actually an HtmlElementCollection object that doesn't have the same methods as an array.

As a result we use `Array.from(images)` (this is something new in ES6) to convert the HtmlElementCollection named "images" to an actual array object. 

We then use the forEach method to loop through the array and for each image, set the image source url to the harambePic url.

To finally use this extension in Chrome, all you have to do is go to `Chrome > Preferences > Extensions`, click on the "Load unpacked extension..." button and navigate to and select the inmemoriam folder. Once that's done, lclick Enable and go to a website of your choice. The pictures on the site should now be Harambe (but there is a possibility it didn't work, more on that later).

With essentially 3 lines of code, we were able to make a simple extension that finds images on a web page, and changes it to a picture of our best friend.

HOWEVER, you might notice that this extension doesn't work all the time, especially on sites like Facebook. When you first navigate to Facebook, all the images you see at the top will be of Harambe, but as you scroll down those other images aren't. This is because Facebook, like many other sites uses something called AJAX (Asynchronous JavaScript and XML) to request data AFTER the initial page load. Because our content script only runs on the first time the page loads, it won't update the rest of the photos as they are being added.

There is a way of solving this, but the code for that is more complex than I would like to make for my first "For Kids" blog post, so more on that in a later tutorial.

If this tutorial was too confusing and not well explained, please leave a comment below and let me know! 



