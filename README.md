# sketch-webview

## Sketch WebView explorations
After Ale’s posting about different approaches to create Sketch dialogues I have investigated in WebViews.

### Success
I was able to open a local HTML page and to execute JavaScript on the page: the HTML page is “www/index.html”. The Sketch script handling the WebView is “webview.jstalk”.

As a side product I wrote a persistence handler based on Sam’s use of the threadDictionary. When I tried to save a JavaScript object I got errors - so I created a persist handler with a get and set method which converts JavaScript elements to/from JSON strings (See file “lib/persistence.jstalk”). This way you can save any element persitently.

#### Some Issues
The communication with the WebView is not possible when the panel with the WebView is created. The communication is possible from the second script run - the reference to the WebView is persistently saved.

#### The WebFrameLoadDelegate
For the direct communication a WebFrameLoadDelegate is needed. I wrote a JavaScript function “WebViewLoadDelegate” which implements the two methods `webView:didClearWindowObject:forFrame:` -> JS function  `WebViewLoadDelegate.prototype.webView_didClearWindowObject_forFrame` and `webView:didFinishLoadForFrame:` -> JS function `WebViewLoadDelegate.prototype.webView_didFinishLoadForFrame`. I set the delegate on the WebView with `setFrameLoadDelegate()`, but the methods are never called. In the “Mocha” documentation I read that JavaScript functions are converted to classes (https://github.com/logancollins/Mocha#javascript-to-objective-c). So the delegate may be ok - I don’t know - and the methods may never be called because the script terminates before the WebView is established and shown.

#### Question: How to implement a WebFrameLoadDelegate in CocoaScript?
#### Question: How can a script continue to run without blocking other threads / Sketch?

#### The JavaScript -> CocoaScript communication
To execute JavaScript in the page with `webView.windowScriptObject().evaluateWebScript(js)`
works perfect starting with the second script run. With the WebView the communication from the JavaScript in the page with ObjC/CocoaScript methods should be possible.

I created a JavaScript object, implemented a property and a method and the `isSelectorExcludedFromWebScript` and 
`isKeyExcludedFromWebScript` methods to open all properties and methods to WebScripting. The JavaScript object is included with `scriptObject.setValue_forKey(runThisFunc, 'callThis’)`, The object ‘callThis’ is visible from the page for JavaScript. But neither the property nor the method are shown nor are they accessible - trying to get the property value or to call the method produce errors.

#### Question: How can a class with properties and methods be created in CocoaScript in a way that the properties and methods are accessible from the page JavaScript?

### So far
I have learned a lot about ObjC and CocoaScript and how a WebView should work. The possibility to execute JavaScript in the WebView is already great and I see a lot of potential here for people working with webdesign/webdevelopment.

To solve the `WebFrameLoadDelegate` and the JavaScript -> CocoaScript communication I need support. It would be great when somebody may find the time to investigate and help solve the issues.

Looking forward to be able to work with HTML pages from Sketch. 
