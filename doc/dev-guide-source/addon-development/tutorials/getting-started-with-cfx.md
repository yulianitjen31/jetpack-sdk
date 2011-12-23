# Getting Started With cfx #

To create add-ons using the SDK you'll have to get to know the `cfx`
command-line tool. It's what you'll use for testing and packaging add-ons.

There's comprehensive
[reference documentation](dev-guide/addon-development/cfx-tool.html) covering
everything you can do using `cfx`, but in this tutorial we'll introduce the
three commands you need to get going:

* [`cfx init`](dev-guide/addon-development/getting-started-with-cfx.html#cfx-init)
: creates the skeleton structure for your add-on
* [`cfx run`](dev-guide/addon-development/getting-started-with-cfx.html#cfx-run)
: runs an instance of Firefox with your add-on installed
* [`cfx xpi`](dev-guide/addon-development/getting-started-with-cfx.html#cfx-xpi)
: build an installable [XPI](https://developer.mozilla.org/en/XPI) file to
distribute your add-on

Note: this tutorial assumes that you've read and followed the instructions in
the [installation guide](dev-guide/addon-development/installation.html), to
install and activate the SDK. If you haven't, go back and do that now, then
come back here.

## <a name="cfx-init">cfx init</a> ##

You use `cfx init` to create the basic skeleton for your add-on.

Create a new directory, navigate to it in your command shell, and run
`cfx init`:

<pre>
mkdir my-addon
cd my-addon
cfx init
</pre>

You don't have to create this directory under the SDK root: once you have
activated from the SDK root, `cfx` will remember where the SDK is, and you
will be able to use it from any directory.

You'll see some output like this:

<pre>
* lib directory created
* data directory created
* test directory created
* doc directory created
* README.md written
* package.json written
* test/test-main.js written
* lib/main.js written
* doc/main.md written

  Your sample add-on is now ready for testing:
      try "cfx test" and then "cfx run". Have fun!"
</pre>

## <a name="cfx-run">cfx run</a> ##

Use `cfx run` to run a new instance of Firefox with your add-on installed.
This is the command you'll use to test out your add-on while developing it.

`cfx init` actually creates a very basic add-on, so to see `cfx run` in action
we don't need to write any code. Just type:

<pre>
cfx run
</pre>

The first time you do this, you'll see a message like this:

<pre>
No 'id' in package.json: creating a new ID for you.
package.json modified: please re-run 'cfx run'
</pre>

<img class="image-right" src="static-files/media/screenshots/widget-mozilla.png"
alt="Mozilla icon widget" />

Run `cfx run` again, and it will run an instance of Firefox. In the
bottom-left corner of the browser you'll see an icon with the Firefox
logo. Click the icon, and a new tab will open with
[http://www.mozilla.org/](http://www.mozilla.org/) loaded into it.

The main code for an add-on is always kept in a file called `main.js` in your
add-on's `lib` directory. Open the `main.js` for this add-on:

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

This add-on uses two SDK modules: the
[`widget`](packages/addon-kit/docs/widget.html) module, which enables you
to add buttons to the browser, and the
[`tabs`](packages/addon-kit/docs/tabs.html) module, which enables you to
perform basic operations with tabs. In this case, we've created a widget
whose icon is the Mozilla favicon, and added a click handler that loads
the Mozilla home page in a new tab.

Try editing this file. For example, we could change the icon displayed
and the URL that gets loaded:

    const widgets = require("widget");
    const tabs = require("tabs");

    var widget = widgets.Widget({
      id: "jquery-link",
      label: "jQuery website",
      contentURL: "http://www.jquery.com/favicon.ico",
      onClick: function() {
        tabs.open("http://www.jquery.com/");
      }
    });

<img class="image-right" src="static-files/media/screenshots/widget-jquery.png"
alt="jQuery icon widget" />

At the command prompt, execute `cfx run` again, and this time the icon is the
jQuery favicon, and clicking it takes you to
[http://www.jquery.com](http://www.jquery.com).

## <a name="cfx-xpi">cfx xpi</a> ##

You'll use `cfx run` while developing your add-on, but once you're done with
that, you use `cfx xpi` to build an [XPI](https://developer.mozilla.org/en/XPI)
file. This is the installable file format for Firefox add-ons. You can
distribute XPI files yourself or publish them to
[http://addons.mozilla.org](http://addons.mozilla.org) so other users can
download and install it.

To build an XPI, just execute the command `cfx xpi` from the add-on's
directory:

<pre>
cfx xpi
</pre>

You should see a message like:

<pre>
Exporting extension to my-addon.xpi.
</pre>

The `my-addon.xpi` file can be found in the directory in which you ran
the command.

To test it, install it in your own Firefox installation.

You can do this by pressing the Ctrl+O key combination (Cmd+O on Mac) from
within Firefox, or selecting the "Open" item from Firefox's "File" menu.

This will bring up a file selection dialog: navigate to the
`my-addon.xpi` file, open it and follow the prompts to install the
add-on.

Now you have the basic `cfx` commands, you can try out the
[SDK's features](dev-guide/addon-development/tutorials.html).