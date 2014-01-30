## Outline

* 1 - [Article Overview](#article_overview)
* 2 - [Backbone Prerequisites](#backbone_prerequisites)
	* 2.1 - You Need Intermediate Knowledge of JavaScript And The DOM
	* 2.2 - You Need Experience With HTTP, XHR, and RESTful JSON APIs
	* 2.3 - Experience With A Functional Programming Library
	* 2.4 - You Need a Loose Understanding Of Models And Views
* 3 - [Application Architecture, Model View Whatever, and Backbone](#application_architecture_model_view_whatever_and_backbone)
	* 3.1 - Architectural Assumptions Made By Backbone 
	* 3.2 - Backbone Doesn’t (and You Shouldn’t) Focus On The Whatever Part Of Model-View-Whatever (i.e. MV*)
	* 3.3 - Backbone Is A Library, Not A Framework
	* 3.4 - Backbone Doesn't Provide Everything Needed To Develop A Single Page, Thick Client, Web Application
* 4 - [Backbone Big Picture Stuff](#backbone_big_picture_stuff)
	* 4.1 - Backbone's Beginnings
	* 4.2 - Backbone Provides Application Structure, As Well As Helpers For Building SPAs 
	* 4.3 - Backbone Hard & Soft Dependencies
	* 4.4 - Backbone Runs On The Server Too
	* 4.5 - `Backbone` Is Just A Toplevel Namespace
* 5 - [Backbone.Events](#backbone.events)
	* 5.1 - Backbone.Events Overview
	* 5.2 - Extending Any Object With Backbone.Events
	* 5.3 - Binding Events to Objects Using `on()`
	* 5.4 - Binding Multiple Events With `on()`
	* 5.5 - Name Spacing Events With `on()`
	* 5.6 - Triggering And Passing Values To A Callback Function By Using `trigger()`
	* 5.7 - Setting The `this` Context For A Callback Function By Using `On()`
	* 5.8 - Removing Events and Callback Functions With `off()`
	* 5.9 - Have An Object Listen for Events On Another Object By Using `listenTo()`
	* 5.10 - Stop Listening For Events On Another Object By Using `stopListening()`
	* 5.11 - Backbone Built-in Events
	* 5.12 - Backbone.Events Conclusion
* 6 - [Constructing Backbone Objects](#constructing_backbone_objects)
	* 6.1 - Backbone Constructor Functions
	* 6.2 - Using the Internal Backbone `extend()` Helper Function to Create Sub Classed Model(), Collection(), Router(), and View() Constructors
	* 6.3 - Running Initialization Code When an Extended Constructor is Instantiated
	* 6.4 - Overriding the Constructor Being Extended
* 7 - [Backbone.Router](#backbone.router)
	* 7.1 - Backbone.Router() Overview
	* 7.2 - Starting Routing Management with `Backbone.history.start()`
	* 7.3 - Backbone Initial Route
	* 7.4 - Defining Routes
	* 7.5 - Running Routes With Wildcard Pathnames (AKA splats or *)
	* 7.6 - Passing Parameters From The URL To The Routes Callback Function
	* 7.7 - Using a Regular Expression With `route()` To Match A URL
	* 7.8 - Routing Broadcasts A Built In `'route:CALLBACK'` Event
	* 7.9 - Manually Navigating Routes By Using `navigate()`
* 8 - [Backbone.History](#backbone.history)
* 9 - [Backbone Utilities](#backbone_utilities)
	* 9.1 - Restore And Create a Unique Reference to Backbone (i.e. custom namespace) With `Backbone.noConflict()`
	* 9.2 - Assign The DOM & XHR Library Used By Backbone With `Backbone.$`
* 10 - [Conclusion](#conclusion)

## 1 - Article Overview

Not a lot of in-depth information from a single voice has been written about Backbone itself.  We have varying degrees of information about how Backbone can be implemented to create an application.  We have lots of opinions about how it does or does not fit into the Model/View/Whatever world. We have more than enough "to-do" demo applications, which can bring a layer of value when learning about application design.  However, what I think is lacking are details about Backbone itself and what the actual code is doing.  The [documentation][1] offers some degree of this as well as the [annotated source][2], but I think [more information][3] is needed. Or, at the very least, a different voice documenting Backbone's function and parts.

In Part 1 and Part 2 of Backbone.js Deconstructed, we are going to examine the function of the Backbone Library at the code level.

Specifically, in Part 1 we will identify some helpful (dare I say 'required'?) prerequisites that make learning Backbone a rewarding endeavor.  Then we will cover some critical details about Single Page Applications (SPAs) that are needed before one can truly understand the purpose and parts of Backbone.  After we have a mental foundation for SPAs, we will dive into the code by systematically breaking down the details around [Backbone.Events][4], creating Backbone Objects, the [Backbone.Router()][5]  and [Backbone.History()][6], and lastly, [Backbone utilities][7].  In Part 2 of Backbone.js Deconstructed, we will systematically uncover the purpose and functionality provided by [Backbone.Model][8], [Backbone.View][9], and [Backbone.Collection][10].

Now, for the part where I you tell you what you won't find in these two articles.  You won't find in-depth information about MVC, MVVM, MVP, or MV-Whatever patterns.  I purposefully have sidestepped any in-depth discussion about these patterns because I believe that Backbone is such a simple library that if you learn the function of the code, the implementation part will be obvious and you will remain free to adapt Backbone to your own domain.

You also won’t find an application being built in this article.  This is commonly the method of teaching Backbone to newcomers and [plenty of approaches in this fashion are available][11].  Additionally, I am not writing this article to produce yet another tutorial that briefly talks about Models and Views.

In this article, we are breaking down the major parts of Backbone and examining the function of each part at the code level.  Expect to get intimate with each part of Backbone.

## 2 - Backbone Prerequisites

**2.1 - You Need Intermediate Knowledge of JavaScript and the DOM**

Backbone abstracts a great deal of the repetitive tasks that can be accomplished with native DOM and HTML5 JavaScript APIs.  Because of this, if you are not experienced with the DOM or with JavaScript, learning about a solution like Backbone might prove to be rather difficult, slow, and frustrating.  Consider that Backbone itself reaches out for a third party solution (i.e. jQuery, Ender, or Zepto) for DOM manipulation and that is two layers of abstraction to get lost in if you do not already have a firm grip on native DOM scripting and modern JavaScript APIs (e.g. XHR).  If you are not at least an intermediate JavaScript developer, you will mostly likely struggle with learning Backbone, as well as my words on the matter.

**2.2 - You Need Experience with HTTP, XHR, and RESTful JSON APIs**

Backbone comes with some helpers that make saving/syncing data to the backend trivial.  Of course, a JSON API is assumed, so if you are not familiar with [HTTP][12], [XHR][13], or reading and writing data to the server by using a [RESTful][14] [JSON][15] API, you might not clearly understand what Backbone is doing for you, or its value in doing it so that you don't have to.

**2.3 - Experience with a Functional Programming Library**

When Backbone was authored, a supporting library was created along with it, called [Underscore.js][16], which provides the functional programming utilities used by Backbone.  This library is a separate tool, which can be used without Backbone, but Backbone has a hard dependency on Underscore.js.  Having experience with a functional programming utility library like Underscore.js or [Lo-Dash.js][17] will help you not only understand the Backbone code better, it will also help you understand the functional methods that are inherited and usable on the Backbone [Model][18] and [Collection][19] objects.  If you have not spent any time gleaning the programming goodness found in Underscore.js or Lo-Dash.js, I highly suggest you spent some time in the trenches of one of these libraries.

**Notes:** 

 - The functional programming utility library [Lo-Dash.js][20] is a [drop-in replacement for Underscore.js][21].
 - At the very least, before you proceed learning Backbone, you
   should have a solid understanding of exactly what the utility method
   [extend()][22] is doing.  It is the most used utility method in the Backbone source code. If you prefer Lo-Dash.js, keep in mind that the Lo-Dash.js library calls this [assign()][23] and `extend()` is an alias for `assign()`.

**2.4 - You Need a Loose Understanding of Models and Views**

Backbone strives to alleviate the “tangled piles of jQuery selectors and callbacks, all trying frantically to keep data in sync between the HTML UI”, and in the right hands, it does just that.  However, it is just as easy to create a tangled pile of Backbone Events, Models, Collections, and Views.  One can always just jump in and thrash about until they figure out how to swim in the Backbone pool, but a general understanding of a model and view will aid a developer when it comes to learning and implementing Backbone.  Unfortunately, gaining even a loose conceptual understanding of application architecture patterns and sub-design patterns is challenging.  I would recommend, at the very least, a general understanding of a model and view without getting tied up in a flavor of architecture for grounding the concept of a model or view.  If you are starting with zero knowledge about a model or a view, I am going to offer a short Backbone-biased definition for each.  If you feel strongly that you already have a conceptual understanding of a model and view, skip this section so I don't taint your previous knowledge.

**Model** - A model contains the state (i.e. the JSON data or what Backbone calls attributes) for an application, as well as a basic set of functionality (i.e. methods like [.set()][24] or [.get()][25] and events like [change][26]) for managing and listening to changes in the state/data.  Additionally, the model orchestrates the storing of this state/data in memory in the browser, as well as persisting the state/data to the database on the server (i.e. syncing).  An ordered grouping of models is a collection.  In Backbone, collections are wrapped with a basic set of functionality for working with groups of models.  The classic illustration of a model would be the data for a to-do item (i.e. the to-do and its state of completion), and a list of to-dos would be considered a collection of to-do models.  If you think in terms of a table, the table would be the collection (i.e. to-dos), the headers in the table would be the model (i.e. title, status), and the content in the table rows would be the data (i.e. get a dog, done).

<table class="table table-bordered">
	<tr>
		<th colspan="2">todo's (i.e. collection of to-do models)</th>
	</tr>
	<tr>
		<th>title (i.e. model attribute)</th>
		<th>status (i.e. model attribute)</th>
	</tr>
	<tr>
		<td>get a dog (i.e. model attribute value)</td>
		<td>done (i.e. model attribute value)</td>
	</tr>
</table>

**View** - A view contains the logic to render an interactive UI of the state/data contained in models and collections of models.  Views allow for state/data to be decoupled from the DOM so that multiple views can make use of the same models and collections.  Thus, if you change a model or a model contained in a collection, multiple views can listen for these changes and update accordingly.  So, ["the truth"][27], or the state/data is not tied to the view; rather, it is decoupled from the view, or specifically, from the DOM/HTML.

## 3 - Application Architecture, Model View Whatever, and Backbone

**3.1 - Architectural Assumptions Made by Backbone** 

Backbone was written for use in the context of developing a SPA (AKA Single Page Application).  You might wonder what that is, exactly.  In short, a SPA is a web architecture which loads a single web page (typically one index.html or app.html) through which the entire application can be delivered to the client by using custom events, custom url handling, and asynchronous HTTP retrieval and storing of resources, all while avoiding traditional browser page reloads.  Basically, a SPA architecture loads one HTML page so that JavaScript can then be used to mimic/override/orchestrate the default nature of the browser that is used in traditional web application architectures.  Let's examine the architectural assumptions of a Backbone SPA in more detail:

 - Backbone SPAs assume application logic (collections, models, views)
   will reside in the client (i.e. browser memory) instead of the
   server.  Because of this, Backbone applications are typically written
   entirely in JavaScript.  Nowadays, even some of the backend parts might be written in JavaScript (e.g. [Node.js][28] provides RESTful API).  
 - Backbone SPAs assume you want to organize the application in a
   loose Model-View-Whatever pattern.
 - Backbone SPAs assume that after the initial synchronous loading of
   the single web page, any additional changes within the application
   will be handled asynchronously and based on custom events and custom
   URLs (i.e. routing URLs defined not by file system hierarchy, but by
   application architecture, logic, and data API).
 - Backbone SPAs typically manage the changes in the URLs and browser history
   with their own routing and history mechanism, therein bypassing the traditional loading and caching of web pages.
 - Backbone SPAs assume data manipulation and persistence (i.e. CRUD or
   "create", "read", "update", or "delete") using a RESTful JSON API.

Single page applications which leverage RESTful JSON APIs are the sweet spot for Backbone.  If you are building something else, or the data API is not set up for REST, you might still find value in the event, model, view, and collection logic provided by Backbone, but you should be aware that you are out of the Backbone sweet spot, given that many Backbone features assume a RESTful JSON API.

**3.2 - Backbone Doesn’t (and You Shouldn’t) Focus on the Whatever part of Model-View-Whatever (i.e. MV*)**

The study of MV-Whatever patterns is not without merit.  However, the study of Backbone in the context of the MV-Whatever family might do more harm than good.  Backbone is an incredibly small library, and the approach one could take to grok a Model-View-Whatever architecture can be accomplished by setting aside this topic altogether and simply studying what Backbone is doing at the code level.  Instead of trying to wrap your head around the very opinionated concepts surrounding the 'Whatever' part of MV-Whatever (i.e. MV*), I recommend instead that you ground your understanding of Backbone in its function and the simple notion that its purpose is to provide aid when organizing application data (models) and UI (views) for SPAs.  I believe that if you learn exactly what the JavaScript is doing, you will naturally start to grok the world of Backbone MV-Whatever.

**3.3 - Backbone is a Library, Not a Framework**

Backbone is a JavaScript library which provides an event system and a set of constructor functions for creating objects that can be used in a MV-Whatever manner to organize and sync data for a single page web application.  The strongest framework-like-opinions built into Backbone are found in its assumption that a SPA architecture and RESTful JSON API are in use.  Remember, a framework calls your code, while a library is code that you call. Backbone, in a nut shell, is a library of constructor functions that you leverage as a developer in order to add structure to your application.  It is not a framework!

**3.4 - Backbone Doesn't Provide Everything Needed to Develop a Single Page, Thick Client, Web Application**

As previously mentioned, Backbone is not a framework.  It is only a piece of the application puzzle.  When building out a single page web application, you will likely require many of the developer tools listed below (the list does not include solutions for building a RESTful JSON API service):

 - A build tool / taskrunner (e.g. [Grunt][29]) 
 - A package manager (e.g. [Bower][30])
 - A dependency manager (e.g. [Require.js][31]) 
 - A UI widget library (e.g. [KendoUI][32])
 - A testing framework/runner (e.g. [Mocha][33]) 
 - A templating solution (e.g. [Handlerbars][34]) 
 - A scaffolding solution (e.g. [Yeoman][35]) 
 - A development server (e.g. [Node.js][36])

If the list above looks like a foreign language to you, then the last thing I would recommend is learning Backbone via a third party Backbone framework that leverages these tools.

Before you use a Backbone framework, research each of the above tools.  Use them and make sure you understand the problem they are solving.  When this is done, check out some of the non-framework [community extensions][37].  Doing this will help you familiarize yourself with some of the gaps other developers have found when using Backbone.

When a proper understanding of Backbone itself has been achieved (along with its gaps), and you understand the problem each of the previously mentioned tools solves, you might find one of the following Backbone toolkits/frameworks useful:
 
 - [Vertebrae][38]
 - [Thorax][39]
 - [Chaplin][40]
 - [Marionette][41]
 - [BBB (Backbone Boilerplate)][42]
 - [Backbone.Giraffe][43]

However, inviting the additional layers of abstraction provided by these toolkits should be done with extreme caution.  These solutions provide a great deal of boilerplate, opinions, and plenty of rope to hang yourself.  I would only reach for one of these once you have a thorough understanding of Backbone and the underlining tools that these frameworks may use.

## 4 - Backbone Big Picture Stuff

**4.1 - Backbone's Beginnings**

Backbone was conjured up specifically to add structure to single page applications (to be exact, it was constructed during the creation of the [DocumentCloud][44] application) so as to avoid the repetitive DOM glue and mixing of data within the DOM that had historically been written to keep the UI in sync with the data.  Backbone accomplishes this by providing a set of constructor functions which produce a model, collection, and view object, all for the purpose of organizing application data, logic, and presentation.  These objects, once instantiated, have a very specific relationship to each other and are intended to keep your application modular, decoupled (communicating using an event system), and scalable.

**4.2 - Backbone Provides Application Structure, as Well as Helpers for Building SPAs** 

In addition to the `Backbone.Model()`, `Backbone.Collection()`, and `Backbone.View()` constructor functions (i.e. the meat), Backbone additionally provides the following helpers for building SPAs and saving data with a RESTful JSON API:

 - Router logic (i.e. `Backbone.Router`), so that specific functions can be
   invoked when the URL changes.
 - Router history logic (i.e. `Backbone.History`), so that the correct
   route occurs when the user uses the browser back or forward button.
   Remember, this is a single page application; traditional URLs are not
   used.
 - Syncing logic (i.e. `Backbone.Sync`), which aids in the reading and
   saving of data to the server.
 - An Event (i.e. `Backbone.Event`) system that facilitates the   
   communication between models, views, and collections through the use
   of custom events and event listeners.
 - A functional JavaScript library (i.e. Underscore.js or
   Lo-Dash.js).

These helper parts, just mentioned, should be understood before attempting to grok models, collections, and views.  And, in fact, this is exactly what I have set out to accomplish in Part 1 of this article.

**4.3 - Backbone Hard & Soft Dependencies**

The only hard dependency identified by the Backbone documentation is the use of a functional programming utility library, either Underscore.js or Lo-Dash.js.  Don’t get hung up here on which to choose, just choose one and use it, or Backbone literally won’t work.  If you want to be told which to use, use Lo-Dash.js.

In addition to either Underscore.js or Lo-Dash.js, Backbone has soft dependencies on json2.js and your choice of [jQuery][45], [Zepto][46], or [Ender][47] (really anything that mimics the jQuery API will work).  If the soft dependency on a DOM library is not included or `window.JSON` is not provided by the browser the `Backbone.Router()`, `Backbone.View()`, `Backbone.History()`, and `Backbone.Sync()` won't function properly.  That is, if you don’t include the soft dependencies, Backbone only offers partial use of models and collections.  I suppose that might be helpful to someone, but it's my opinion that 99 percent of the time, the soft dependencies will be included along with Backbone.js (depending upon what browsers you need to support).

An example HTML file that details the Backbone dependencies might look something like this:

    <!DOCTYPE html>
    <html lang="en">
    <body>
    <!-- functional programming utility library -->
    <script src="http://cdnjs.cloudflare.com/ajax/libs/lodash.js/1.3.1/lodash.underscore.js"></script>
    <!-- DOM & XHR utility -->
    <script src="http://cdnjs.cloudflare.com/ajax/libs/jquery/2.0.2/jquery.min.js"></script>
    <!-- needed for older browsers without window.JSON -->
    <script src="http://cdnjs.cloudflare.com/ajax/libs/json2/20121008/json2.js"></script>
    <!-- Backbone.js -->
    <script src="http://cdnjs.cloudflare.com/ajax/libs/backbone.js/1.0.0/backbone-min.js"></script>
    </body>
    </html>

It is common, if not required, that you use a dependency manager when building out a web application (e.g. [Require.js][48]).  The HTML example file above is not a recommendation.  Rather, it is being shown to clearly communicate the Backbone hard and soft dependencies.  My opinion is that a dependency manager should be used when building a single page, thick client-side, web application.

**4.4 - Backbone Runs on the Server Too**

Backbone is useful anywhere you need to structure an application.  This includes server-side applications, as well.  The developers behind Backbone have crafted the library so that it can be [downloaded from NPM][49] for Node.js server-side application development.  However, its origins started in the client, and using it on the server will require some additional knowledge about running client-centric solutions from a JavaScript backend environment.

**4.5 - `Backbone` is Just a Top-Level Namespace**

The `Backbone` object that is exposed in the global scope is a namespace which contains Backbone code.  Specifically, it contains the  `Model()`, `View()`, `Collection()`, `History()`, and `Router()` constructor functions, as well as some supporting logic (i.e. `Events` and `sync()`) for these constructors.  The code can be summarized like this:

    (function(){
    
        var root = this;
   
        Backbone.VERSION = '1.0.0';
        
        Backbone.Events = {//events functions to mixed into any object};
        Backbone.Model = function() {//model constructor};
        Backbone.Collection = function() {//collection constructor};
        Backbone.Router = function() {//router constructor};
        Backbone.History = function() {//history constructor};
        Backbone.sync = function() {//sync function};
        Backbone.View = function() {//view constructor};
        
    }).call(this);

## 5 - Backbone.Events

**5.1 - Backbone.Events Overview**

The `Backbone.Events` object contains methods that facilitate the [observer design pattern and a variation of the observer pattern called pub/sub][50]. These functions permit the various objects created by Backbone (really any JavaScript object) to communicate with each other in a decoupled manner.  This is done by extending the prototypes (i.e. `_.extend(Model.prototype, Backbone.Events);`) of the following constructor functions with the functions contained in `Backbone.Events`:

 - `Backbone.Model()`
 - `Backbone.Collection()`
 - `Backbone.View()`
 - `Backbone.Router()`
 - `Backbone.History()`

If the observer design pattern is meaningless to you and the term pub/sub is a rather mussy concept, just think of the functions contained in `Backbone.Events` similar to functionality provided by the jQuery `$('').on()`, `$('').off()`, `$('').trigger()`, and `$('').one()` methods for attaching native or custom events to DOM objects with the added ability to listen to events as well.

The `Backbone.Events` object contains the following functions which create events, trigger events, and create listening relationships between objects based on events.

 - `on(event, callback, context)` or `bind()` alias works too
 - `off(event, callback, context)` or `unbind()` alias works too
 - `trigger(event, arguments)` 
 - `once(event, callback, context)` 
 - `listenTo(other object, event, callback)` 
 - `stopListening(other object, event, callback)` 
 - `listenToOnce(other object, event, callback)` 

In addition to extending the prototype of each of the above constructor functions, the `Backbone` namespace object itself is extended (i.e. `_.extend(Backbone, Backbone.Events);`) with the properties found in `Backbone.Events` so that a global eventing system is available if needed.  For example, below I created a generic `sayHi` event and an `eavesdropper` object to listen for any `sayHi` events that are triggered on the Backbone object.

$[http://jsfiddle.net/FcxPc/][js,result]

**Notes:**

 - Keep in mind that while I just demonstrated the use of
   `Backbone.Events` in the context of the `Backbone` object, the
   original intention of `Backbone.Events` was to extend the previously
   mentioned Backbone constructor functions with a pattern that would
   allow decoupled communication.

**5.2 - Extending Any Object With Backbone.Events**

As previously mentioned, the `Backbone.Events` object properties are mixed into the prototype chain of several of the constructor functions provided by Backbone, as well as the `Backbone` namespace object.  Of course, nothing is stopping you from extending any object with the functions found in the `Backbone.Events` object. `Backbone.Events` basically is a standalone set of functions that can be mixed into any old JavaScript object.  Below I demonstrate this by extending the `A` object and then verifying that the `Backbone.Events` methods are now properties of `A`.

$[http://jsfiddle.net/QqvCJ/][js,result]

**5.3 - Binding Events to Objects using `on()`**

The `on()` method will bind a callback function to an object that will be invoked whenever the event is triggered.  In the code below I bind a `whatsMyName` event to the `A` object and then trigger that event.

$[http://jsfiddle.net/eRP2w/][js,result]

**Notes:**

 - A `once(event,callback,context)` method is available that works just
   like `on()` except the event and callback function will be removed
   immediately after being invoked, causing the event and callback to
   occur only once.

**5.4 - Binding Multiple Events with `on()`**

The `on()` accepts a string event parameter which can contain multiple events separated by a space.  Below I add two events to the `A` object, which call the same function, and then I trigger this callback with the two different event names.

$[http://jsfiddle.net/b25KK/][js,result]

**5.5 - Name Spacing Events with `on()`**

It is possible to namespace events by using a colon in the event name followed by the namespace.  Below I have added two `say` events with a unique namespace, which I then use to invoke these name spaced events.

$[http://jsfiddle.net/35qXs/][js,result]

**5.6 - Triggering And Passing Values To A Callback Function Using `trigger()`**

The `trigger()` method is used to invoke named (i.e. events) callback functions.  Its use has been prevalent in this section.  The second parameter passed to the `trigger()` method gives the option of passing values to the callback function.  Below I trigger a callback function on `A` to which I pass several values.

$[http://jsfiddle.net/RnaRY/][js,result]

Notes: 

 - Multiple events can be triggered by providing a space-delimited string as the first parameter to `trigger()` listing each event you'd like to invoke (i.e. `obj.trigger('event1 event2 event3');`). 
 - You can trigger an event on an object that's not actually defined on that object and any object listening for this event will still be notified that the event has been triggered.

**5.7 - Setting The `this` Context For A Callback Function Using `on()`**

The third parameter that can be passed to `on()` allows the `this` context for the callback function to be set.  In the code below I invoke a callback on `A` but tell the value of `this` in the callback to be `B`.

$[http://jsfiddle.net/PwuXv/][js,result]

**5.8 - Removing Events and Callback Functions with `off()`**

The `off()` method can be used in the following 5 ways to remove individual events, callback functions, or all events from an object.

1 - Removing a named callback function:

$[http://jsfiddle.net/MAF8P/][js,result]

2 - Removing all occurrences of an event:

$[http://jsfiddle.net/pqkJA/][js,result]

3 - Removing all the occurrences of a named callback function from all events:

$[http://jsfiddle.net/hYrP7/][js,result]

4 - Removing all events for a specific context:

$[http://jsfiddle.net/qv5Cs/][js,result]

5 - Removing all events and callbacks on an object:

$[http://jsfiddle.net/Ks8S4/][js,result]

**Notes:**

 - Using `off()` with no parameters on a Backbone model, collection, view, or router object will remove everything including the built in BackBone events and callbacks.

**5.9 - Have an object listen for events on another object using `listenTo()`**

The `listenTo()` method gives the ability for one object to listen for an event on another object, and when that event occurs provide a callback function that is invoked.  In the code example below I tell object `B` to listen for the `whosListeningToMe` event to occur on object `A`, and when it does occur invoke the `whosListeningToMe` callback function.

$[http://jsfiddle.net/AAp5c/][js,result]

**Notes:**

 - A `listenToOnce(other,callback,callback)` method is available that
   works just like `listenTo()` except the callback function will be
   removed immediately after being invoked, causing the listen to occur
   only once.
 - When an event is triggered on an object, regardless of if the event
   has a handler on that object, any object listening for the event is
   still notified that the event was triggered.

**5.10 - Stop listening for events on another object using `stopListening()`**

The `stopListening()` method can be used on objects that are listening to other objects in the following four ways:

1 - Stop All Listening

$[http://jsfiddle.net/sBzLu/][js,result]

2 - Stop listening to a specific object

$[http://jsfiddle.net/cym8q/][js,result]

3 - Stop listening to a specific event on a specific object

$[http://jsfiddle.net/dnhbW/][js,result]

4 - Stop Listening to a specific event and callback on a specific object

$[http://jsfiddle.net/EpRjw/][js,result]

**5.11 - Backbone Built-in Events**

Backbone triggers the following internal events that have been built in to models, collections, views, and the router objects.  Because Backbone triggers these events we can make use of these events by way of the `on()` and `listenTo()` Backbone event methods.  More on that in a minute.  For now, just make sure you review the description of individual events in the table below so you start to gain an understanding of when these events are triggered.

**model, collection, view, router, and history object events**
<table class="table table-bordered">
	<tr>
		<th align="center" >event type</th>
		<th align="center" >arguments passed to callback</th>
		<th align="center" >description</th>
	</tr>
	<tr>
		<td><code>'all'</code></td>
		<td>event name</td>
		<td>this special event fires for <em>any</em> (i.e. not just built in events) triggered event, passing the event name as the first argument</td>
	</tr>
</table>

**collection object events**
<table class="table table-bordered">
	<tr>
		<th align="center" >event type</th>
		<th align="center" >arguments passed to callback</th>
		<th align="center" >description</th>
	</tr>
	<tr>
		<td><code>'add'</code></td>
		<td> model, collection, options</td>
		<td>when a model is added to a collection</td>
	</tr>
	<tr>
		<td><code>'remove'</code></td>
		<td> model, collection, options</td>
		<td>when a model is removed from a collection</td>
	</tr>
	<tr>
		<td><code>'reset'</code></td>
		<td>collection, options</td>
		<td>when the collection's entire contents have been replaced.</td>
	</tr>
	<tr>
		<td><code>'sort'</code></td>
		<td> collection, options</td>
		<td>when the collection has been re-sorted</td>
	</tr>
</table>

**model object events**

<table class="table table-bordered">
	<tr>
		<th align="center" >event type</th>
		<th align="center" >arguments passed to callback</th>
		<th align="center" >description</th>
	</tr>
	<tr>
		<td><code>'change'</code></td>
		<td>model, options</td>
		<td>when a model's attributes have changed.</td>
	</tr>
	<tr>
		<td><code>'change:[attribute]'</code></td>
		<td>model, value, options</td>
		<td>when a specific attribute has been updated.</td>
	</tr>
	<tr>
		<td><code>'destroy'</code></td>
		<td>model, collection, options</td>
		<td>when a model is <a href="http://backbonejs.org/#Model-destroy">destroyed</a>.</td>
	</tr>
	<tr>
		<td><code>'error'</code></td>
		<td>model, xhr, options</td>
		<td>when a model's <a href="http://backbonejs.org/#Model-save">save</a> call fails on the server.</td>
	</tr>
	<tr>
		<td><code>'invalid'</code></td>
		<td>model, error, options</td>
		<td>when a model's <a href="http://backbonejs.org/#Model-validate">validation</a> fails on the client.</td>
	</tr>
</table>

**model or collection object events**
<table class="table table-bordered">
	
	<tr>
		<th align="center" >event type</th>
		<th align="center" >arguments passed to callback</th>
		<th align="center" >description</th>
	</tr>
	<tr>
		<td><code>'request'</code></td>
		<td>model, xhr, options</td>
		<td>when a model or collection has started a request to the server.</td>
	</tr>
	<tr>
		<td><code>'sync'</code></td>
		<td>model, resp, options</td>
		<td>when a model (or collection) has been successfully synced with the </td>
	</tr>
</table>

**router object events**
<table class="table table-bordered">
	<tr>
		<th align="center" >event type</th>
		<th align="center" >arguments passed to callback</th>
		<th align="center" >description</th>
	</tr>
	<tr>
		<td><code>'route:[name]'</code></td>
		<td>params</td>
		<td>Fired by the router when a specific route is matched.</td>
	</tr>
	<tr>
		<td><code>'route'</code></td>
		<td>router, route, params </td>
		<td>Fired by history (or router) when <em>any</em> route has been matched.</td>
	</tr>
</table>

The above events, except for the `'all'` event, will be examined in more detail in the section that deals with the object that the event is triggered on.  To gain an understanding about Backbone built in events we will examine the `'all'` event now.

Built in events are triggered by Backbone objects.  The `'all'` event is triggered/broadcasted anytime any event is triggered on a Backbone model, collection, view, or router. 

In the code below I instantiate a Backbone model then I bind (i.e `on()`) a callback to the `'all'` event.  Next, I set some data on the model.  When this data is set, the model will broadcast two events, a `'change'` event as well as a `'change:attribute'` event (You can verify this in the table above).  The `'all'` event gets fired twice because it's broadcasted when events, in this case two `'change'` events, are triggered. 

$[http://jsfiddle.net/svPq6/][js,result]

The all event captures, as its first argument, the event that triggered the `'all'` event.  This allows us to log which event triggered the `'all'` event. 

Don't forget, the `'all'` event is not unique to models it is triggered anytime any of the built in Backbone events are triggered.  However, note that the `'change'` events are specific to models.  This example should lead the way for understanding broadly how all the built in events can be tapped into when developing Backbone applications.


**5.12 - `Backbone.Events` Conclusion**

The take away from this section is that the concepts used by Backbone objects to communicate with each other are generic patterns.  By understanding the generic uses provided by Backbone.Events for any JavaScript object you will be better equipped to understand how to create your own events, as well as make use of the events that are internally triggered by Backbone Models, Collections, Views, and the Router.

## 6 - Constructing Backbone Objects

**6.1 - Backbone Constructor Functions**

When I claimed earlier that Backbone itself is simple, I did so because Backbone only provides 4 constructor functions that are typically instantiated (technically, they are extended or sub-classed first by a developer before instantiation). 

These four constructors are:

    Backbone.Model = function(attributes, {options}){};
    Backbone.Collection = function([models], {options}){};
    Backbone.Router = function({options}){}; //typically instantiated once 
    Backbone.View = function({options}){};

Let's examine the default instance and prototype properties for each of the instances created from the above constructor functions by simply instantiating each with the `new` keyword (click on the results in the JSfiddles to view instance and prototype properties) passing no parameters.

Models:

$[http://jsfiddle.net/NNyug/][js,result]

Collection:

$[http://jsfiddle.net/jbsEG/][js,result]

Router:

$[http://jsfiddle.net/2XczH/][js,result]

View:

$[http://jsfiddle.net/AaJGx/][js,result]

The properties identified for each constructor function are what Backbone provides out of the box.  However, it is more common that you will be extending (i.e. `Backbone.Model.extend()`) these constructors with your own domain specific instance and prototype properties and methods before you construct them.

Briefly, before we continue, let's examine the optional parameters for each constructor. More time will be spent on these parameters in the appropriate section but I think it's important to have a rough understanding of the optional parameters.

    var myModel = new Backbone.Model(attributes, {options});
    /*
    attributes = {data:value, data:value} || [value,value]
    
    options = {
     collection: {}, 
     url: '', 
     urlRoot: '', 
     parse: boolean
    }
    */
    
    
    var myCollection = new Backbone.Collection(model(s), {options});
    /*
    models = model || [model,model,model] || {model:data} || [{model:data},{model:data}];
    
    options = {
     url: '', 
     model: {}, 
     comparator: function(){} || ''
    }
    */
    
    
    var myView = new Backbone.View({options});
    /*
    options = {
     model: {}, 
     events: {} or function(){return {}}
     collection: {}, 
     el: '' or function(){return ''}, 
     id: '', 
     className: '' or function(){return ''}, 
     tagName: '' or function(){return ''}, 
     attributes: {attribute:value,attribute:value}
    }
    */
    
    
    var myRouter = new Backbone.Router({options});
    /*
    options = {
     routes:{}
    }
    */

    /*should be aware of History options as well*/
    Backbone.History.start({options});
    /*
    options = {
     pushState: boolean,
     root: '',
     hashChange: boolean,
     silent: boolean
    }
    */


**Notes:**

 - The `Router()` constructor can be instantiated more than once but given the assumption of a SPA typically its only instantiated once.
 - The `Backbone.History()` constructor function is [internally
   constructed by the library itself][51], one time, when the library is
   parsed.
 - Notice that all of the instances, as previously mentioned, have the
   `Backbone.Events` mixed into the prototypes of each constructor.

**6.2 - Using The Internal Backbone `extend()` Helper Function To Create Sub Classed Model(), Collection(), Router(), and View() Constructors.**

The Backbone pattern for constructing a model, collection, view, or router routinely involves extending one of the default Backbone constructor functions (creating a sub class or sub constructor) with your own domain relevant properties and methods.  This is accomplished with the [`extend()` function][52] that is [a property of the constructor function][53].  Don't confuse this function with the `extend()` provided by underscore.js. 

The `extend()` provided by Backbone accomplishes, from a high level, the following: 

 - Duplicates the instance values of the constructor function its
   extending
 - Extends the new sub constructor with developer defined prototype   
   properties and static properties
 - Facilitates the prototypical inheritance between the constructor
   being extended and the new constructor (sub-constructor)
 - Adds the `extend()` helper function to the new sub constructor
   function so that the sub constructor can also be extended to create
   an unlimited chain of sub classes
 - Institutes an `initialize()` callback to be called when the
   sub-constructor creates instances and sets the context of the
   initialize function.

For a more detailed break down of how the Backbone `extend()` method works, carefully read the comments in the code below which describes the logic contained in the `extend()` function for extending a fictitious `View` constructor (similar but not precisely `Backbone.View`).  While this is not the exact code contained in Backbone.js its an isolated example of what is occuring at the code level when a `Backbone.Model`, `Backbone.Collection`, `Backbone.View`, and `Backbone.Router` constructor is sub classed (or what I like to call an extended sub constructor) .

$[http://jsfiddle.net/Ysd8w/][js,result]

Understanding `extend()`, and its role when using Backbone will take a lot of the mystery out of Backbone applications.  I would suggest you examine its parts until you completely understand its purpose and function, but if you want to move past this subject quickly what you need to know about extend() boils down to one concept.

 1. `extend()`, creates or extends an object with new application
    specific object properties so that when the extended object is
    instantiated it will have these new instance properties and methods.

**6.3 - Running Initialization Code When An Extended Constructor Is Instantiated**

When `extend()`'ing `Backbone.Model`, `Backbone.Collection`, `Backbone.View`, and `Backbone.Router` an `initialize` function can be setup to run anytime an instance is created from the extended constructor.  Basically, initialize is a callback, which is invoked any time an instance is instantiated.  In the code below I provide an initialize callback for an extended `Backbone.Model` constructor and then create an instance, which in turn runs the initialized function (works the same on `Backbone.Collection`, `Backbone.View`, and `Backbone.Router`). 

$[http://jsfiddle.net/e9PFh/][js,result]

Make sure you note that any parameters passed to an extended constructor are also passed into the initialize callback.

$[http://jsfiddle.net/TsqAt/][js,result]

**Notes:**

 - The context of the initialize function (i.e. `this`) is set to the instance created.
 - When extending a Backbone constructor you have to be aware of what
   parameters and values the constructor permits/requires when
   instantiating an instance.  For example, the `Backbone.Model` constructor expects as the first parameter an object containing the data to be stored in
   the Model (i.e. attributes).  The second parameter is an object
   containing instantiation options. You can add additional parameters
   and they are simply passed on to the initialize function.

**6.4 - Overriding The Constructor Being Extended**

When using `extend()` if you pass a property called 'constructor' with a function as its value, this function will be used instead of the function the `extend()` is a property of.  In the code below I am extending not the `Model` constructor, but the constructor function I am passing in.

$[http://jsfiddle.net/gBjXx/][js,result]

In the code above `myModel` still inherits the `Model()` prototype properties, just not the instance properties (i.e. `cid`).  If you override the constructor, but want to keep instance properties from the constructor you are calling `extend()` from, you need to run `Backbone.[CONSTRUCTOR].apply(this, arguments);` in the custom constructor*/

$[http://jsfiddle.net/wpvBX/][js,result]

## 7 - Backbone.Router

**7.1 - Backbone.Router() Overview**

A Backbone route is a JavaScript string, which resembles what traditionally has been considered a URL [pathname][54].  This pathname-like string is the property name for a function (or reference to a named function) that is invoked when the url in the browser matches the string.  For example, the url below contains the pathname "help" and in a single page Backbone application this would tell Backbone to invoke the function that is associated with the route called 'help'.

    http://www.hostname.com/#help

Notice that the pathname "help" isn't actually a [pathname][55], it is actually a [hash][56].  Backbone defaults to using hash paths because as of today its the most widely supported solution for creating unique URL strings without causing a browser to reload.  When you no longer need to support ie 9 or below you can tell Backbone to use the newer HTML5 [history.pushState][57] which eliminates the need for hash paths.  The `history.pushState` api updates the browser history and permits the modification of the URL pathname without causing the browser to reload.  I will talk more about this later in this section.  For now, let's look at an actual Backbone route.

In the code below I create an instance of the default `Backbone.Router()` and then define a "help" route using the `route()` method.

$[http://jsfiddle.net/gn3kL/][js,result]

In the code example above when the hash changes to `#help` Backbone is aware of this change and takes everything after the `#` and matches that to a predefined string name (i.e. "help") which is associated with a callback function.

Routes are essentially pathname like strings in a URL that are monitored by Backbone for string matches.  When a match is found a corresponding function is invoked.

**Notes:**

- Routes by default rely on the [hashchange][58] event and the hash portion of a url. If Backbone is configured to use `history.pushState()` then the [popstate][59] event and normal pathnames are used if available, but will default back to hash's if its not.  If Backbone detects that neither of these events are available it will resort to [polling][60] the url and checking to see if the URL has changed.

**7.2 - Starting Routing Management with `Backbone.history.start()`**

To have Backbone begin managing url changes (i.e. call default route, listen for routes, manage browser history, and deal with back button) you will need to call the `Backbone.history.start()`.  The details for this method can be found in the section that talks about `Backbone.History`. For now, just realize that Backbone has to manually tell the browser to start the routing process.  In the code below I setup a 'help' route but a change to the url is not monitored by Backbone until `Backbone.history.start()` is invoked.

$[http://jsfiddle.net/kvABu/][js,result]

**7.3 - Backbone Initial Route**

The initial route is the route that runs when `Backbone.history.start()` is invoked.  The initial route is defined by sending the router an empty string route name signifying that this route should run by default (i.e. the root) when routing begins.  In the code example below I define an initial route that is called after `Backbone.history.start()` is invoked.

$[http://jsfiddle.net/zSGKQ/][js,result]

**Notes:**

 - To kick off routing without the initial route being invoked pass the
   `{silence:true}` to the `.start()` method.

**7.4 - Defining Routes**

Up to this point the routing code examples have been defining routes using the `route()` method after the instantiation of a `Backbone.Router()` instance. To be comprensive, defining routes can occur not just by way of the `route()` method but in the following four ways (including the `route()` method):

1 - When Extending `Backbone.Router` Using `routes` Option:

$[http://jsfiddle.net/3Qsak/][js,result]

2 - When Instantiating A Router Using `routes` Option:

$[http://jsfiddle.net/2yaFA/][js,result]

3 - After An Instance Is Created Using The route() Method:

$[http://jsfiddle.net/VqnDC/][js,result]

4 - During Initialization Of An Instance Using The route() Method:

$[http://jsfiddle.net/HTufd/][js,result]

**7.5 - Running Routes With Wildcard Pathnames (aka splats or *)**

A wildcard pathname can be used to indicate that any pathname will cause the route to run.  For example, the route `'*anyURL'` will run anytime a url is changed.

$[http://jsfiddle.net/X72zF/][js,result]

And the route `'/help/*'` will run anytime the url changes to `'help/ANYTHING/ANYTHING'`.

$[http://jsfiddle.net/z8yyy/][js,result]

**Notes:** 

 - Passing a splat route alone (i.e. `'*'`), today, causes Backbone to throw an error.

**7.6 - Passing Parameters From The URL To The Routes Callback Function**

To identify url parts that should be considered parameters simply place a `:` in front of the name of the parameter contained in the route string. For example in the code below I pass the search route callback function, 4 parameters.

$[http://jsfiddle.net/T5MYJ/][js,result]

Notes: 

 - If you surround the url parameters in `()` then this becomes an optional parameter, without the `()` the url will only run if it contains all of the parameters defined in the route string.

**7.7 - Using A Regular Expression With `route()` to Match A URL**

The `route()` method optionally accepts as its first parameter a regular expression value instead of a string value.  This can be pretty handy when you'd like to finely tune the matching of urls.  In the code example below I demonstrate the matching of a route for a specific range of words.

$[http://jsfiddle.net/yUdwb/][js,result]

**7.8 - Routing Broadcasts A Built In `'route:CALLBACK-NAME'` Event**

When a route is match and the callback function is called a built in `'route`' event, with a namespace referring to the name of the callback invoked is dispatched/broadcasted by Backbone.  The `on()` or `listenTo()` event methods can be used to tap into the `'route:NAMEOFCALLBACKFUNCTION'` event.

In the code below I am using `on()` or `listenTo()` to monitor and invoke additional callback functions when the `search` callback is invoked.

$[http://jsfiddle.net/47mL9/][js,result]

Notice that both `on()` or `listenTo()` pass along any url parameters.

**Note:** 

 - In addition to the built int `'route:CALLBACK-NAME'` on route objects there is also a `'route'` event that gets dispatched anytime a route has been matched.

**7.9 - Manually Navigating Routes using `navigate()`**

Up to this point I've mostly been invoking routes in the code example by changing the `window.location.hash` value in the browser, which Backbone is monitoring.  By using the `navigate()` method we can accomplish the same thing.  In the code below instead of update the hash value in the browser I am going to invoke the `navigate()` method passing it the route string to navigate too.

$[http://jsfiddle.net/tAPn9/][js,result]

**Notes:**

 - By default, `navigate()` will update the url in the browser (and deal with history and back button) but not invoke the route callback function.  To update the url in the browser and invoke the callback associated with the route you must pass the `trigger:true` option to `navigate()`.
 - By default, `navigate()` assumes you want to change the url and   
   update the browser's history.  If you'd prefer not to update the
   browser history (which is used by the back button) you can pass
   the replace:true option to `navigate()`.  This will simply replace
   the current url without updating the browsers history.

## 8 - Backbone.History

The `Backbone.Router` is responsible for managing routes.  The `Backbone.history` is part of routing and responsible for listening and responding to  URL changes, including updates to the browsers history. The `Backbone.History()` constructor is instantiated by the Backbone library itself and an instance of `History()` is [referenced from][61] `Backbone.history`.  The object that is placed at `Backbone.history` has one method named `start()`.  Calling this method tells Backbone to begin listening for routes and to start managing the browser history.  This method takes an options object with the following options and values.

    Backbone.history.start({
    
    /*Boolean, default is false, true means use pushState if available and fallback to hash paths if needed.*/
    pushState: true,
    
    /*Boolean, default is true, a value of false if pushState is true means that browsers that don't support pushState will do a traditional browser reload based on URL pathname.  If pushState is false and hashChange is false then url changes will do a traditional browser reload based on URL pathname. */
    hashChange: false,
    
    /*String, default is '' and backbone assumes you are serving if from the root url (i.e. /).  A value other than '' is a string path to the directory your application is being server from and the directory Backbone should consider the root directory. */ 
    root:'',
    
    /* Boolean, default is false, a true value means don't run the default route (i.e.''). */
    silent: true
        
    });

You'll likely never create a `History()` instance in your code, but keep in mind that you'll almost always need to invoke `Backbone.history.start()` in order to begin monitoring hashchange events and invoke route callbacks.

**Notes:**

 - Due to a reliance on iframes to make hash paths work in IE its best
   if `start()` is invoked after the DOM is ready.
 - When calling `start()` the first time, it will run the initial route.
   The initial route is the current url when `.start()` is called, which in
   turn invokes the route that is associated with any empty string
   property name.
 - You don't have to have all of your routes defined before you call
   `start()`. If you don't use an initial route you can define all
   routes after `start()` with the `route()` method available on `Backbone.Router()` instances.
 - It's not exactly intuitive, but routing is not started from a
   `Backbone.router()` instance its started from the
   `Backbone.history()` instance. Just remember that router objects
   define routes and provides a `navigate()` method but in order for
   routing to start for each web page loaded into a browser, you have to
   invoke `Backbone.history.start()`.

## 9 - Backbone Utilities

**9.1 - Restore and Create A Unique (i.e. custom namespace) Reference to Backbone with `Backbone.noConflict()`**

When Backbone is being parsed by the browser, one of the first things Backbone will do is store a reference to the value contained at `Backbone` in the Global scope (i.e. typically at `window.Backbone`).  This is because Backbone will overwrite or take over this namespace and wants to give the developer the option to restore the previous value used before the Backbone library is parsed.  This is where the `Backbone.noConflict()` function comes into play.  Calling it will return the `Backbone` namespace to the previous owner and then return a reference to the last parsed version of Backbone.js so you can create a new namespace for it.  In the code below I create a fictitious Backbone version before Backbone.js is loaded 

    <script>
        
        this.Backbone = {
        "I'm the previous owner of Backbone in the global scope": true
        };
        
    </script>

and use `Backbone.noConflict()` to make sure this version remains reference from Backbone while giving the last parsed version of Backbone a new namespace.

**Notes:**

 - If you call `Backbone.noConflict()` and don't store the reference value returned from the function call you've lost a reference to the last Backbone.js code that was parsed by the browser.

$[http://jsfiddle.net/zLEUu/][js,result]

**9.2 - Assign the DOM & XHR library used by Backbone with `Backbone.$`**

[Backbone will internally and automatically use any version of jQuery, Zepto, or ender (in that order) found in the global scope for DOM manipulation and XHR (aka AJAX)][62] by storing a reference to one of these libraries at `Backbone.$`. 

If none of these libraries are found in the global scope, Backbone will attempt to use whatever value is found in the global scope for `$`, which could be `undefined` if you don't include one of the previously mentioned libraries or define your own library which uses `$` as a namespace or alias.

One can also manually provide a value for `Backbone.$` after Backbone has been parsed.  In the code example below I demonstrate how `Backbone.$` can be set by either method.

    <!DOCTYPE html>
    <html lang="en">
    <body>
    <!-- functional programming utility library -->
    <script src="http://cdnjs.cloudflare.com/ajax/libs/lodash.js/1.3.1/lodash.underscore.js"></script>
    <!-- needed for older browsers without window.JSON -->
    <script src="http://cdnjs.cloudflare.com/ajax/libs/json2/20121008/json2.js"></script>
    <!-- DOM & XHR utility -->
    <script>
    //Backbone will use this if jQuery, Zepto, and ender don't claim $
    var $ = {version:'0.0.1',name:'MyReCreateTheWheelDOM&AJAXLib'}
    </script>
    <!-- Backbone.js -->
    <script src="http://cdnjs.cloudflare.com/ajax/libs/backbone.js/1.0.0/backbone-min.js"></script>
    <!-- DOM & XHR utility -->
    <script>
    //Or just Manually set it after Backbone is loaded
    Backbone.$ = {version:'0.0.1',name:'MyReCreateTheWheelDOM&AJAXLib'}
    </script>
    </body>
    </html>

**10 - Conclusion**

[I've read and watched a great deal of tutorials on Backbone.js][63] and none of them provided a comprehensive foundation (all the parts around the meat) for grokking the function of a `Backbone.Model`,`Backbone.Collection`, and `Backbone.View`.  It was my intention to alleviate this gap of information with the contents of this article.

With a thorough Backbone backdrop set into place by this article we will move on to the main stage actors, `Backbone.Model`,`Backbone.Collection`, `Backbone.View` and `Backbone.sync` in the second part of this article.

> Now Available: [Part 2: Backbone.js Deconstructed][64]


  [1]: http://backbonejs.org/
  [2]: http://backbonejs.org/docs/backbone.html
  [3]: http://lostechies.com/derickbailey/2011/12/27/the-responsibilities-of-the-various-pieces-of-backbone-js/
  [4]: http://backbonejs.org/#Events
  [5]: http://backbonejs.org/#Router
  [6]: http://backbonejs.org/#History
  [7]: http://backbonejs.org/#Utility
  [8]: http://backbonejs.org/#Model
  [9]: http://backbonejs.org/#View
  [10]: http://backbonejs.org/#Collection
  [11]: http://pluralsight.com/training/courses/TableOfContents?courseName=backbone-fundamentals
  [12]: http://net.tutsplus.com/tutorials/tools-and-tips/http-the-protocol-every-web-developer-must-know-part-1/
  [13]: https://developer.mozilla.org/en-US/docs/AJAX/Getting_Started
  [14]: http://www.restapitutorial.com/lessons/whatisrest.html
  [15]: https://developer.mozilla.org/en-US/docs/JSON
  [16]: http://underscorejs.org/
  [17]: http://lodash.com/
  [18]: http://backbonejs.org/#Model-Underscore-Methods
  [19]: http://backbonejs.org/#Collection-Underscore-Methods
  [20]: http://lodash.com/
  [21]: https://raw.github.com/bestiejs/lodash/v1.3.1/dist/lodash.underscore.js
  [22]: http://underscorejs.org/#extend
  [23]: http://lodash.com/docs#assign
  [24]: http://backbonejs.org/#Model-set
  [25]: http://backbonejs.org/#Model-get
  [26]: http://backbonejs.org/#Events-catalog
  [27]: http://www.infoq.com/presentations/Getting-Truth-Out-of-the-DOM
  [28]: http://nodejs.org/
  [29]: http://gruntjs.com/
  [30]: http://bower.io/
  [31]: http://requirejs.org/
  [32]: http://www.kendoui.com/
  [33]: https://github.com/visionmedia/mocha
  [34]: http://handlebarsjs.com/
  [35]: http://yeoman.io/
  [36]: http://nodejs.org/
  [37]: https://github.com/jashkenas/backbone/wiki/Extensions,-Plugins,-Resources
  [38]: https://github.com/tbranyen/vertebrae
  [39]: http://thoraxjs.org/
  [40]: http://chaplinjs.org/
  [41]: http://marionettejs.com/
  [42]: https://github.com/backbone-boilerplate/grunt-bbb
  [43]: http://barc.github.io/backbone.giraffe/
  [44]: https://www.documentcloud.org/home
  [45]: http://jquery.com/
  [46]: http://zeptojs.com/
  [47]: http://ender.jit.su/
  [48]: http://requirejs.org/
  [49]: https://npmjs.org/package/backbone
  [50]: http://msdn.microsoft.com/en-us/magazine/hh201955.aspx
  [51]: http://backbonejs.org/docs/backbone.html#section-188
  [52]: http://backbonejs.org/docs/backbone.html#section-190
  [53]: http://backbonejs.org/docs/backbone.html#section-196
  [54]: https://developer.mozilla.org/en-US/docs/Web/API/window.location
  [55]: https://developer.mozilla.org/en-US/docs/Web/API/window.location
  [56]: http://en.wikipedia.org/wiki/Fragment_identifier
  [57]: https://developer.mozilla.org/en-US/docs/Web/Guide/DOM/Manipulating_the_browser_history
  [58]: https://developer.mozilla.org/en-US/docs/Web/API/window.onhashchange
  [59]: https://developer.mozilla.org/en-US/docs/Web/API/window.onpopstate?redirectlocale=en-US&redirectslug=DOM/window.onpopstate
  [60]: http://backbonejs.org/docs/backbone.html#section-172
  [61]: http://backbonejs.org/docs/backbone.html#section-188
  [62]: http://backbonejs.org/docs/backbone.html#section-9
  [63]: http://readlists.com/f1c7b9ca/
  [64]: http://tech.pro/tutorial/1476/part-2-backbonejs-deconstructed