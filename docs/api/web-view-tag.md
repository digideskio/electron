# The `<webview>` tag

Use the `webview` tag to embed 'guest' content (such as web pages) in your
Electron app. The guest content is contained within the `webview` container.
An embedded page within your app controls how the guest content is laid out and
rendered.

Unlike an `iframe`, the `webview` runs in a separate process than your
app. It doesn't have the same permissions as your web page and all interactions
between your app and embedded content will be asynchronous. This keeps your app
safe from the embedded content.

## Example

To embed a web page in your app, add the `webview` tag to your app's embedder
page (this is the app page that will display the guest content). In its simplest
form, the `webview` tag includes the `src` of the web page and css styles that
control the appearance of the `webview` container:

```html
<webview id="foo" src="https://www.github.com/" style="display:inline-block; width:640px; height:480px"></webview>
```

If you want to control the guest content in any way, you can write JavaScript
that listens for `webview` events and responds to those events using the
`webview` methods. Here's sample code with two event listeners: one that listens
for the web page to start loading, the other for the web page to stop loading,
and displays a "loading..." message during the load time:

```html
<script>
  onload = function() {
    var webview = document.getElementById("foo");
    var indicator = document.querySelector(".indicator");

    var loadstart = function() {
      indicator.innerText = "loading...";
    }
    var loadstop = function() {
      indicator.innerText = "";
    }
    webview.addEventListener("did-start-loading", loadstart);
    webview.addEventListener("did-stop-loading", loadstop);
  }
</script>
```

## Tag Attributes

The `webview` tag has the following attributes:

### `src`

```html
<webview src="https://www.github.com/"></webview>
```

Returns the visible URL. Writing to this attribute initiates top-level
navigation.

Assigning `src` its own value will reload the current page.

The `src` attribute can also accept data URLs, such as
`data:text/plain,Hello, world!`.

### `autosize`

```html
<webview src="https://www.github.com/" autosize="on" minwidth="576" minheight="432"></webview>
```

If "on", the `webview` container will automatically resize within the
bounds specified by the attributes `minwidth`, `minheight`, `maxwidth`, and
`maxheight`. These constraints do not impact the `webview` unless `autosize` is
enabled. When `autosize` is enabled, the `webview` container size cannot be less
than the minimum values or greater than the maximum.

### `nodeintegration`

```html
<webview src="http://www.google.com/" nodeintegration></webview>
```

If "on", the guest page in `webview` will have node integration and can use node
APIs like `require` and `process` to access low level system resources.

### `plugins`

```html
<webview src="https://www.github.com/" plugins></webview>
```

If "on", the guest page in `webview` will be able to use browser plugins.

### `preload`

```html
<webview src="https://www.github.com/" preload="./test.js"></webview>
```

Specifies a script that will be loaded before other scripts run in the guest
page. The protocol of script's URL must be either `file:` or `asar:`, because it
will be loaded by `require` in guest page under the hood.

When the guest page doesn't have node integration this script will still have
access to all Node APIs, but global objects injected by Node will be deleted
after this script has finished executing.

### `httpreferrer`

```html
<webview src="https://www.github.com/" httpreferrer="http://cheng.guru"></webview>
```

Sets the referrer URL for the guest page.

### `useragent`

```html
<webview src="https://www.github.com/" useragent="Mozilla/5.0 (Windows NT 6.1; WOW64; Trident/7.0; AS; rv:11.0) like Gecko"></webview>
```

Sets the user agent for the guest page before the page is navigated to. Once the
page is loaded, use the `setUserAgent` method to change the user agent.

### `disablewebsecurity`

```html
<webview src="https://www.github.com/" disablewebsecurity></webview>
```

If "on", the guest page will have web security disabled.

### partition

```html
<webview src="https://github.com" partition="persist:github"></webview>
<webview src="http://electron.atom.io" partition="electron"></webview>
```

Sets the session used by the page. If `partition` starts with `persist:`, the
page will use a persistent session available to all pages in the app with the
same `partition`. if there is no `persist:` prefix, the page will use an
in-memory session. By assigning the same `partition`, multiple pages can share
the same session. If the `partition` is unset then default session of the app
will be used.

This value can only be modified before the first navigation, since the session
of an active renderer process cannot change. Subsequent attempts to modify the
value will fail with a DOM exception.

## Methods

The `webview` tag has the following methods:

**Note:** The webview element must be loaded before using the methods.

**Example**
```javascript
webview.addEventListener("dom-ready", function() {
  webview.openDevTools();
});
```

### `<webview>.getUrl()`

Returns URL of guest page.

### `<webview>.getTitle()`

Returns the title of guest page.

### `<webview>.isLoading()`

Returns a boolean whether guest page is still loading resources.

### `<webview>.isWaitingForResponse()`

Returns a boolean whether the guest page is waiting for a first-response for the
main resource of the page.

### `<webview>.stop()`

Stops any pending navigation.

### `<webview>.reload()`

Reloads the guest page.

### `<webview>.reloadIgnoringCache()`

Reloads the guest page and ignores cache.

### `<webview>.canGoBack()`

Returns a boolean whether the guest page can go back.

### `<webview>.canGoForward()`

Returns a boolean whether the guest page can go forward.

### `<webview>.canGoToOffset(offset)`

* `offset` Integer

Returns a boolean whether the guest page can go to `offset`.

### `<webview>.clearHistory()`

Clears the navigation history.

### `<webview>.goBack()`

Makes the guest page go back.

### `<webview>.goForward()`

Makes the guest page go forward.

### `<webview>.goToIndex(index)`

* `index` Integer

Navigates to the specified absolute index.

### `<webview>.goToOffset(offset)`

* `offset` Integer

Navigates to the specified offset from the "current entry".

### `<webview>.isCrashed()`

Whether the renderer process has crashed.

### `<webview>.setUserAgent(userAgent)`

* `userAgent` String

Overrides the user agent for the guest page.

### `<webview>.getUserAgent()`

Returns a `String` representing the user agent for guest page.

### `<webview>.insertCSS(css)`

* `css` String

Injects CSS into the guest page.

### `<webview>.executeJavaScript(code, userGesture)`

* `code` String
* `userGesture` Boolean - Default `false`.

Evaluates `code` in page. If `userGesture` is set, it will the create user
gesture context in the page. HTML APIs like `requestFullScreen`, which require
user action, can take advantage of this option for automation.

### `<webview>.openDevTools()`

Opens a DevTools window for guest page.

### `<webview>.closeDevTools()`

Closes the DevTools window of guest page.

### `<webview>.isDevToolsOpened()`

Returns a boolean whether guest page has a DevTools window attached.

### `<webview>.inspectElement(x, y)`

* `x` Integer
* `y` Integer

Starts inspecting element at position (`x`, `y`) of guest page.

### `<webview>.inspectServiceWorker()`

Opens the DevTools for the service worker context present in the guest page.

### `<webview>.setAudioMuted(muted)`

* `muted` Boolean

Set guest page muted.

### `<webview>.isAudioMuted()`

Returns whether guest page has been muted.

### `<webview>.undo()`

Executes editing command `undo` in page.

### `<webview>.redo()`

Executes editing command `redo` in page.

### `<webview>.cut()`

Executes editing command `cut` in page.

### `<webview>.copy()`

Executes editing command `copy` in page.

### `<webview>.paste()`

Executes editing command `paste` in page.

### `<webview>.pasteAndMatchStyle()`

Executes editing command `pasteAndMatchStyle` in page.

### `<webview>.delete()`

Executes editing command `delete` in page.

### `<webview>.selectAll()`

Executes editing command `selectAll` in page.

### `<webview>.unselect()`

Executes editing command `unselect` in page.

### `<webview.replace(text)`

* `text` String

Executes editing command `replace` in page.

### `<webview>.replaceMisspelling(text)`

* `text` String

Executes editing command `replaceMisspelling` in page.

### `<webview>.print([options])`

Prints `webview`'s web page. Same with `webContents.print([options])`.

### `<webview>.printToPDF(options, callback)`

Prints webview's web page as PDF, Same with `webContents.printToPDF(options, callback)`

### `<webview>.send(channel[, args...])`

* `channel` String
* `arg` (optional)

Send `args..` to guest page via `channel` in asynchronous message, the guest
page can handle it by listening to the `channel` event of `ipc` module.

See [WebContents.send](browser-window.md#webcontentssendchannel-args) for
examples.

## DOM events

The following DOM events are available to the `webview` tag:

### Event: 'load-commit'

Returns:

* `url` String
* `isMainFrame` Boolean

Fired when a load has committed. This includes navigation within the current
document as well as subframe document-level loads, but does not include
asynchronous resource loads.

### Event: 'did-finish-load'

Fired when the navigation is done, i.e. the spinner of the tab will stop
spinning, and the `onload` event is dispatched.

### Event: 'did-fail-load'

Returns:

* `errorCode` Integer
* `errorDescription` String
* `validatedUrl` String

This event is like `did-finish-load`, but fired when the load failed or was
cancelled, e.g. `window.stop()` is invoked.

### Event: 'did-frame-finish-load'

Returns:

* `isMainFrame` Boolean

Fired when a frame has done navigation.

### Event: 'did-start-loading'

Corresponds to the points in time when the spinner of the tab starts spinning.

### Event: 'did-stop-loading'

Corresponds to the points in time when the spinner of the tab stops spinning.

### Event: 'did-get-response-details'

Returns:

* `status` Boolean
* `newUrl` String
* `originalUrl` String
* `httpResponseCode` Integer
* `requestMethod` String
* `referrer` String
* `headers` Object

Fired when details regarding a requested resource is available.
`status` indicates socket connection to download the resource.

### Event: 'did-get-redirect-request'

Returns:

* `oldUrl` String
* `newUrl` String
* `isMainFrame` Boolean

Fired when a redirect was received while requesting a resource.

### Event: 'dom-ready'

Fired when document in the given frame is loaded.

### Event: 'page-title-set'

Returns:

* `title` String
* `explicitSet` Boolean

Fired when page title is set during navigation. `explicitSet` is false when title is synthesised from file
url.

### Event: 'page-favicon-updated'

Returns:

* `favicons` Array - Array of Urls.

Fired when page receives favicon urls.

### Event: 'enter-html-full-screen'

Fired when page enters fullscreen triggered by HTML API.

### Event: 'leave-html-full-screen'

Fired when page leaves fullscreen triggered by HTML API.

### Event: 'console-message'

Returns:

* `level` Integer
* `message` String
* `line` Integer
* `sourceId` String

Fired when the guest window logs a console message.

The following example code forwards all log messages to the embedder's console
without regard for log level or other properties.

```javascript
webview.addEventListener('console-message', function(e) {
  console.log('Guest page logged a message:', e.message);
});
```

### Event: 'new-window'

Returns:

* `url` String
* `frameName` String
* `disposition` String - Can be `default`, `foreground-tab`, `background-tab`,
  `new-window` and `other`

Fired when the guest page attempts to open a new browser window.

The following example code opens the new url in system's default browser.

```javascript
webview.addEventListener('new-window', function(e) {
  require('shell').openExternal(e.url);
});
```

### Event: 'close'

Fired when the guest page attempts to close itself.

The following example code navigates the `webview` to `about:blank` when the
guest attempts to close itself.

```javascript
webview.addEventListener('close', function() {
  webview.src = 'about:blank';
});
```

### Event: 'ipc-message'

Returns:

* `channel` String
* `args` Array

Fired when the guest page has sent an asynchronous message to embedder page.

With `sendToHost` method and `ipc-message` event you can easily communicate
between guest page and embedder page:

```javascript
// In embedder page.
webview.addEventListener('ipc-message', function(event) {
  console.log(event.channel);
  // Prints "pong"
});
webview.send('ping');
```

```javascript
// In guest page.
var ipc = require('ipc');
ipc.on('ping', function() {
  ipc.sendToHost('pong');
});
```

### Event: 'crashed'

Fired when the renderer process is crashed.

### Event: 'gpu-crashed'

Fired when the gpu process is crashed.

### Event: 'plugin-crashed'

Returns:

* `name` String
* `version` String

Fired when a plugin process is crashed.

### Event: 'destroyed'

Fired when the WebContents is destroyed.
