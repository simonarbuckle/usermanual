Title: URL handling


This article will guide you through services provided in Aria Templates to handle URLs.  The first part focuses on mapping files paths to URLs, the second part details how to create custom URLs to communicate with your server.

## Classpaths and URL mapping

Aria Templates' default behavior is to load classes using the Download Manager (`aria.core.DownloadMgr`) based on these steps:


* The classpath is turned into a physical path by replacing (i.e. the template `foo.bar.Main` becomes `foo/bar/Main.tpl`.)

* The resulting path is appended to AT's root folder.

URL mapping provides a way to modify this behavior.  It may be useful in two cases:


* You need to specify that a class identified by a given classpath + extension is in a specific file.

* You need to specify that a given package is located somewhere else.

### Mapping a class or a package to a file

Let's say you want to force the definition of the `foo.bar.BarClass` Javascript class to be fetched from `foo/bar/AnotherBarClass.js` instead of `foo/bar/BarClass.js`; you would then write:

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/appendices/URLhandling.js?tag=urlmap1&outdent=true&noheader=true&lang=js'></script>

You could also specify that all classes of a package are available in one file like this:

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/appendices/URLhandling.js?tag=urlmap2&outdent=true&noheader=true&lang=js'></script>

In the example above, any class which classpath starts with `foo.bar` is expected to be declared inside the bar.js file.

### Mapping the URL root path

You can define what to use as a root path for a given package using the `updateRootMap()` method:

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/appendices/URLhandling.js?tag=rootmap&outdent=true&noheader=true&lang=js'></script>

In this example:

* `foo.bar.Main` will be retrieved in `/newroot/foo/bar/Main.js`

* `foo.specialclasses.Main` will be retrieved in `/specialroot/foo/specialclasses/Main.js`

You can use `updateUrlMap` in conjunction with `updateRootMap` to create custom URLs.

### Advanced mapping

UrlMap and RootMap accept function references and instances of `aria.utils.Callback` in their definition. Such a function must be defined as follows:


* An **urlMap** callback receives one argument: the logical path to a file (e.g. `foo/bar/MyClass.js`) and it must return the new path (relative to rootUrl.)

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/appendices/URLhandling.js?tag=urlmapper1&outdent=true&noheader=true&lang=js'></script>

* A **rootMap** callback also receives the logical path to a file (e.g. `bar/MyClass.js`) and must return the new root path to be used.

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/appendices/URLhandling.js?tag=rootmapper1&outdent=true&noheader=true&lang=js'></script>

The callbacks can be used this way:

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/appendices/URLhandling.js?tag=urlmapper2&outdent=true&noheader=true&lang=js'></script>

or using anonymous functions:

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/appendices/URLhandling.js?tag=urlmapper3&outdent=true&noheader=true&lang=js'></script>

<div style="background:#FAFFDD;border:1px solid #EFFAB4;border-radius:3px;color:#666;font-size:12px;padding:2px 5px;"><strong>Note:</strong> Classic callback descriptions, i.e. `{ fn : myMethod, scope : myScope }` cannot be used here as they would result in non-determinist maps structures.  You can however define such callbacks using the `aria.utils.Callback` class.</div>

Example:

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/appendices/URLhandling.js?tag=urlmapperCB&outdent=true&noheader=true&lang=js'></script>

## Server requests URL handling

In order to be as independent as possible from the server-side framework, Aria Templates provides a simple way to build the URLs used for [server requests](request_from_a_controller) and [retrieving resources](localization_and_resources): the URL Creation Service.

To define the way URLs are created, you need to create a class implementing the [aria.modules.urlService.IUrlService](http://ariatemplates.com/api/#aria.modules.urlService.IUrlService) interface and its two methods:


* **`createActionUrl`**: used to create URLs used for actions.  This method is passed the following parameters:

	* `moduleName`: relative path of the package from which the request occurs, e.g. `foo/bar` if the request is done from foo.bar.MyClass
	*`actionName`: the name of the action given in the [RequestObject](http://ariatemplates.com/api/#aria.modules.RequestBeans:RequestObject) passed to the request
	*`sessionId`: the id, if any, given to the [setSession()](http://ariatemplates.com/api/#aria.templates.ModuleCtrl:setSession:method) method

* **`createI18nUrl`**: the same for resources for this module...

Here is a sample method from an urlService class to create the call URL:

<script src='http://snippets.ariatemplates.com/snippets/github.com/ariatemplates/documentation-code/snippets/modules/controller/MyUrlService.js?tag=actionUrl&lang=javascript&outdent=true'></script>

<div style="background:#FAFFDD;border:1px solid #EFFAB4;border-radius:3px;color:#666;font-size:12px;padding:2px 5px;"><strong>Note:</strong> `Aria.rootFolderPath` contains the path to the root of the framework, it can be used to build the server path.</div>