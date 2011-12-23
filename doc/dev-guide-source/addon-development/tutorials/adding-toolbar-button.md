# Adding a Button to the Toolbar #

To add a button to the toolbar, use the
[`widget`](packages/addon-kit/docs/widget.html) module.

The default add-on created by `cfx init`
uses a widget, so we'll start with that as an example. If you haven't already
followed the tutorial introducing
[`cfx init`](dev-guide/addon-development/tutorials/getting-started-with-cfx.html#cfx-init),
do that now, then come back here.

The `lib/main.js` file looks like this:

    const widgets = require("widget");
    const tabs = require("tabs");

    var widget = widgets.Widget({
      id: "mozilla-link",
      label: "Mozilla website",
      contentURL: "http://www.mozilla.org/favicon.ico",
      onClick: function() {
        tabs.open("http://www.mozilla.org/");
      }
    });

The widget is added to the "Add-on Bar" at the bottom of the browser window:

<img src="static-files/media/screenshots/widget-mozilla.png"
alt="Mozilla icon widget" />

You can't change the initial location for the widget, but the user can move
it to a different toolbar.

Clicking the button opens [http://www.mozilla.org](http://www.mozilla.org).

## Specifying the Icon ##

If you're using the widget to make a toolbar button, specify the icon to
display using `contentURL`: this may refer to a remote file as in the
example above, or may refer to a local file. The example below will load
an icon file called "my-icon.png" from the add-on's `data` directory:

    const widgets = require("widget");
    const tabs = require("tabs");
    const self = require("self");

    var widget = widgets.Widget({
      id: "mozilla-link",
      label: "Mozilla website",
      contentURL: self.data.url("my-icon.png"),
      onClick: function() {
        tabs.open("http://www.mozilla.org/");
      }
    });

You can change the icon at any time by setting the widget's `contentURL`
property.

## Responding To the User ##

You can listen for `click`, `mouseover`, and `mouseout` events by assigning
functions to the corresponding widget properties. The widget example above
assigns a listener to the `click` event using the `onClick` option, and
there are similar `onMouseover` and `onMouseout` options.

To handle user interaction in more detail, you can attach a content
script to the widget. Your add-on script and the content script can't
directly access each other's variables or call each other's functions, but
they can send each other messages.

Here's an example. The widget's built-in `onClick` property does not
distinguish between left and right mouse clicks, so to do this we need
to use a script. The script looks like this:

    this.addEventListener('click', function(event) {
      if(event.button == 0 && event.shiftKey == false)
        self.port.emit('left-click');

      if(event.button == 2 || (event.button == 0 && event.shiftKey == true))
        self.port.emit('right-click');
        event.preventDefault();
    }, true);

It uses the standard DOM `addEventListener()` function to listen for click
events, and handles them by sending the corresponding message to the main
add-on code. Note that the messages "left-click" and "right-click" are not
defined in the widget API itself, they're custom events defined by the add-on
author.

Save this script in your `data` directory as "click-listener.js".

Next, modify `main.js` to:

<ul>
<li>pass in the script by setting the <code>contentScriptFile</code>
property</li>
<li>listen for the new events:</li>
</ul>

    const widgets = require("widget");
    const tabs = require("tabs");
    const self = require("self");

    var widget = widgets.Widget({
      id: "mozilla-link",
      label: "Mozilla website",
      contentURL: "http://www.mozilla.org/favicon.ico",
      contentScriptFile: self.data.url("click-listener.js")
    });

    widget.port.on("left-click", function(){
      console.log("left-click");
    });

    widget.port.on("right-click", function(){
      console.log("right-click");
    });

Now execute `cfx run` again, and try right- and left-clicking on the button.
You should see the corresponding string written to the command shell.

(You'll also see the standard add-on bar context menu appear when you
right-click: this is a
[bug in the SDK](https://bugzilla.mozilla.org/show_bug.cgi?id=626326).)

## Attaching a Panel ##

If you supply a `panel` object to the widget's constructor, then the panel
will be shown when the user clicks the widget:

    data = require("self").data

    var clockPanel = require("panel").Panel({
      width:215,
      height:160,
      contentURL: data.url("clock.html")
    });

    require("widget").Widget({
      id: "open-clock-btn",
      label: "Clock",
      contentURL: data.url("History.png"),
      panel: clockPanel
    });

<!-- The icon the widget displays, shown in the screenshot, is taken from the
Nuvola icon set, http://www.icon-king.com/projects/nuvola/ which is made
available under the LGPL 2.1:
http://www.gnu.org/licenses/old-licenses/lgpl-2.1.html -->

<img class="image-center" src="static-files/media/screenshots/widget-panel-clock.png"
alt="Panel attached to a widget">
<br>

## Window-Specific Widgets ##
