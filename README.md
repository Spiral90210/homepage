# Homepage

This is a static replacement for a browser homepage, with just the links I want. Designed to be loaded locally (which presents some challenges) rather than needing a http request, it's to stop 'helpful' homepages based on history etc. which are just not configurable (looking at you Firefox...).

These settings can follow you around tp computers with different usernames. I use macOS and recommend you set up a symlink `/opt/homepage` on each system pointing to the cloned repository so the browser config is the same for all. I'm not sure how this works across different OS with profiles that follow you, YMMV.

I should just set up a file server, but then it's something else to look after isn't it.

## Setup

These instructions assume you have this repo cloned to `/opt/homepage` or have a symlink there pointing to the real location.

### Safari

Safari lets you set a local file as a homepage, so just set it to `file:///opt/homepage/index.html`

### Chrome

Chrome requires a plugin to set a homepage to a local file (probably because local runs with elevated privileges and they cba to support this because then it's not online and that's not google). I've used [Custom New Tab URL](https://chrome.google.com/webstore/detail/custom-new-tab-url/mmjbdbjnoablegbkcklggeknkfcjkjia) but you can use whatever

### Firefox

Disclaimer: I use FF as my primary. The "browser of the people". But want to set a simple local home page? HAHAHA. You need to disable sandboxing and do this via an autoconfig script. Yup. AND it will follow your profile around, even on to systems that may not have that file and don't have the config script! So be warned, this could do untold damage.

Note these instructions are for macOS. When you see `/Applications/Firefox.app/Contents/Resources` you can replace this with the 'application root path' on whatever OS you are running

- Create an autoconfig file here `/Applications/Firefox.app/Contents/Resources/defaults/pref/autoconfig.js`. Make sure to include the first comment line.

```javascript
// enable autoconfig
pref("general.config.filename", "firefox.cfg");
pref("general.config.obscure_value", 0); // default is to expect the content to be 13 bytes offset for obscurity
pref("general.config.sandbox_enabled", false); // :'( stupid I know
```

- Then create the config file in the Resources directory `/Applications/Firefox.app/Contents/Resources/firefox.cfg`, again being careful about the first comment.

```javascript
// first line is a comment
var { classes: Cc, interfaces: Ci, utils: Cu } = Components;
try {
  Cu.import("resource:///modules/AboutNewTab.jsm");
  var newTabURL = "file:///opt/homepage/index.html";
  AboutNewTab.newTabURL = newTabURL;
} catch (e) {
  Cu.reportError(e);
} // report errors in the Browser Console
```
