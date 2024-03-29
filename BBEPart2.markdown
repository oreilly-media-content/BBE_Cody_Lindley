* 1 - [Article Overview](#article_overview)
* 2 - [Backbone.View](#backbone.view)
	* 2.1 - Conceptual Overview Of A `Backbone.View`
	* 2.2 - Subclassing and Creating a `Backbone.View`
	* 2.3 - Configuring A `Backbone.View` With Options
	* 2.4 - `Backbone.View` Methods, Properties, And Events
	* 2.5 - Running `initialize()` Function When Backbone View's Are Instantiated
	* 2.6 - Rendering A View
	* 2.7 - Connecting A View To An Element Node
	* 2.8 - Setting Element Attributes On An Element Node In Memory
	* 2.9 - Using The jQuery-Wrapped `el` Shortcut (aka `this.$el`)
	* 2.10 - Using the jQuery-Wrapped `el` Scoped Shortcut (aka `this.$()`)
	* 2.11 - Setting Delegated Events For A Backbone View Using The `events` Property
	* 2.12 - Setting The Element Node Associated With A View Using `setElement()`
	* 2.13 - Removing A View From The DOM Using `remove()`
	* 2.14 - Attaching Delegated Events Using `delegateEvents()`
	* 2.15 - Using Templates With Views
* 3 - [Backbone.Model](#backbone.model)
	* 3.1 - Conceptual Overview Of A `Backbone.Model`
	* 3.2 - Subclassing and Creating a `Backbone.Model`
	* 3.3 - `Backbone.Model` Methods, Properties, And Events
	* 3.4 - Setting Default Model Values/Attributes
	* 3.5 - Setting, Verifying, Getting, UnSetting, and Clearing Model Data
	* 3.6 - Accessing A Models Data/Attributes
	* 3.7 - Getting A Copy Of A Models Data/Attributes Using `toJSON()`
	* 3.8 - Filtering Model Data
	* 3.9 - Listening To Model `change` Events
	* 3.10 - Verify if a Model Has Changed And What Changed Using `hasChanged()` and `changedAttributes()`
	* 3.11 - Getting Previous Attribute Values Using `previous()` and `previousAttributes()`
	* 3.12 - Validating Model Data Before Its Set or Saved
	* 3.13 - Listening To A Model's `invalid` Event
	* 3.14 - Manually Run `validate` On Model
* 4 - [Backbone.Collection](#backbone.collection)
	* 4.1 - Conceptual Overview Of A `Backbone.Collection`
	* 4.2 - Creating a `Backbone.Collection` From Model Instances
	* 4.3 - Creating a `Backbone.Collection` From A Model Constructor
	* 4.4 - `Backbone.Collection` Methods, Properties, And Events
	* 4.5 - Getting All Model Data/Attributes Out Of A Collection Using `toJSON()`
	* 4.6 - Getting Models From A Collection Using `models`, `get()`, `at()`, `where()`, and `findWhere()`
	* 4.7 - Sorting A Collection
	* 4.8 - Getting an Array of Model Attributes And Values From Each Model in a Collection Using `Pluck()`
	* 4.9 - Adding Models To A Collection Using `add()`, `push()`, `unshift()`
	* 4.10 - Removing Models From A Collection Using `remove()`, `pop()`, `shift()`
	* 4.11 - Add, Merge, and Remove Models At The Same Time Using `set()`
	* 4.12 - Replacing All Models In (i.e. bulk replace/reset) A Collection Using `reset()`
	* 4.13 - Using underscore.js (or lodash.js) Methods On Models (i.e. `models`) In A Collection
* 5 - [Building A Simple Contacts Application](#building_a_simple_contacts_application)
	* 5.1 - Overview
	* 5.2 - Extend `Backbone.Model` creating `Contact` Constructor/Class
	* 5.3 - Instantiate `contacts` Collection, Passing it Model Constructor/Class And A Contact
	* 5.4 - Extend `Backbone.View` creating `AddContactsView` & Instantiate An Instance
	* 5.5 - Extend `Backbone.View` creating `ContactListView` & Instantiate An Instance
	* 5.6 - Working Demo
* 6 - [Conclusion](#conclusion)

## 1 - Article Overview

In Part 1 of [Backbone.js Deconstructed][1] an in-depth review of the periphery Backbone parts was completed. If you haven't read part 1, I'd suggest you start with part 1 before proceeding with part 2. At the very least I would make sure you are comfortable with the concepts discussed in [part 1, section 6, Constructing Backbone Objects][2].

In part 2 of Backbone.js Deconstructed, we will be examining the meat of Backbone. The meat being Backbone, views, models, and collections. Equipped with the knowledge conveyed in part 1, you should be adequately prepared to get intimate with `Backbone.View`, `Backbone.Model` and `Backbone.Collection` constructors and corresponding object instances. Before we begin, I am going to lay out my strategy for discussing the meat.

While it is common that most efforts to teach Backbone start with learning about models, I actually think this prohibits learning. I believe once the peripheral parts are understood, the easiest path for grokking Backbone starts with the view. I strongly suggest a mastery of views before you attempt to populate a view with data (i.e. models or collections of models) and eventing the view to stay in sync with the data. The plan is to first examine views, then models, then collections in that order. Once each of these parts are understood, we will take this knowledge and use it to examine a [small and contrived contact's application][3]. Don't be afraid to examine [the application][4] first, In fact I recommend it, as it will give a helpful context before reading sections 2, 3, and 4. 

> I have decided to write a Part 3 in this article series focusing on
> the syncing of model and collection data using a restful JSON api
> (i.e. fetch, save, destroy, sync). I did this to keep the complexity
> surrounding syncing (i.e. servers and api's) out of this article with
> the hope that this will shine a spotlight on the nature and function
> of views, models, and collections.

Let's get to it!

## 2 - Backbone.View

**2.1 - Conceptual Overview Of A `Backbone.View`**

It is common to consider a view as an isolated region of a web application. In other words, any logical chunk of the UI, when cutting up the UI into smaller parts for development, can be considered a view. Thus, think of a `Backbone.View` as the container (i.e. object) for the JavaScript logic that renders and updates this isolated region. Note that a `Backbone.View` does not store data or groups of data; it is commonly the hub that references model(s) or collection(s) of data and does something with that data.

If it helps, think of the view as the location in your code that brings together and controls the following parts:

 - data (i.e. models or collection's of models)
 - HTML template
 - reference/hook into DOM element that contains the HTML markup forming the UI
 - events and functions driving the UI

The views' job, then, is to take these parts (i.e. data, template, DOM, events) and render the UI region.

Here are some points to drill into your mind about Backbone views.

 - Backbone views contain all of the parts necessary to construct an
   isolated and logical region of a web application. `Backbone.View` objects
   glue together data, templates, events and the rendering and
   re-rendering logic that occurs when data changes.
 - Views do not have to be connect to a model or a collection (i.e.
   data).  A view can simply manage an isolated and logical region of a
   web application.
 - It is common practice for a view to reference a template (i.e. the
   html). This might be a stretch for some considering that views
   typically are the template. This is not the case with Backbone views.

**2.2 - Subclassing and Creating a `Backbone.View`**

To create a generic view, we only need to instantiate an instance from `Backbone.View`.

    var myView = new Backbone.View();

However, it is more likely that you will be extending/subclassing the base `Backbone.View` before you instantiate an instance so that you can add your own domain specific properties.

Below I extend the `Backbone.View` constructor creating a sub-constructor (i.e. subclass) and define some contrived domain specific properties for instances to inherit.

$[http://jsfiddle.net/NTtjE/][js,result]

**Notes:**

 - View instances have an internal `cid` property (e.g. `myViewInstance.cid`), which is a unique identifier automatically assigned to view when they are created. This value is [derived][5] from the `_.uniqueId('view')` method.

**2.3 - Configuring A `Backbone.View`** With Options

When you are extending or instantiating a view, you can pass the following **special** properties, which are hijacked and purposed by Backbone:

    //Extending a view
    
    var MyView = new Backbone.View.extend({
    
    model: {}, 
    events: {} || function(){return {}}
    collection: {}, 
    el: '' || function(){return ''}, 
    id: '', 
    className: '' || function(){return ''}, 
    tagName: '' || function(){return ''}, 
    attributes: {'attribute':'value','attribute':'value'}
    
    });
    
    //Instantiating a view
    
    var myView = new Backbone.View({
    
    model: {}, 
    events: {} || function(){return {}}
    collection: {}, 
    el: '' || function(){return ''}, 
    id: '', 
    className: '' || function(){return ''}, 
    tagName: '' || function(){return ''}, 
    attributes: {'attribute':'value','attribute':'value'}
    
    });

When the above special view options are passed as options during instantiation, they become properties of the view instance as well as properties of the `options` property. In the code below I demonstrate access to both.

$[http://jsfiddle.net/YWn3f/][js,result]

You should be aware that non-special (i.e. foo) options only show up in the `options` property.

**2.4 - `Backbone.View` Methods, Properties, And Events**

Backbone view instances have the following [methods and properties][6]:

 - el 
 - $el
 - setElement()
 - attributes
 - $()
 - render()
 - remove()
 - delegateEvents()
 - undelegateEvents()

We will be examining all of the above methods and properties in this section.

Additionally, views can make use of the following built-in event:

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

**2.5 - Running `initialize()` Function When Backbone Views Are Instantiated**

All view instances when instantiated will invoke an internal initialize function that can be defined when extending `Backbone.View`. Below we include an initialize function that runs when a view instance is created.

$[http://jsfiddle.net/FsPA7/][js,result]

The initialize function is an ideal place to house logic that needs to run when view instances are created.

**Notes:** 

 - The value of `this` inside of the initialize function is scoped to
   the `myViewInstance` that is created.
 - The initialize function commonly houses events which listen for changes on models and collections.

**2.6 - Rendering A View**

View objects come with a default [render()][7] method that is intended to be overwritten. The render function, by design, is intended to house the logic required to construct the view. In the code below I make use of the initialize function to call the instances render function when it is instantiated.

$[http://jsfiddle.net/RNFQK/][js,result]

Not much rendering is occurring because we have yet to connect the view to an element node, but clearly the `render` function should contain the logic that constructs a view.

**Notes:**

 - It is not a bad idea to `return this` inside of the render function so
   that you can chain view methods. All of the built-in Backbone view
   methods follow this practice.

**2.7 - Connecting A View To An Element Node**

Backbone views are intended to be tied to an [element node][8] which is literally either in the html producing the UI or in memory. Typically, when instantiating a view, either the `el` or `tagname`  property is set connecting the view instance to an element node. Below I demonstrate both of these scenarios.

Connecting a view to an element node in the HTML page using a CSS selector:

$[http://jsfiddle.net/hjbTR/][js,html,result]

Connecting a view to an element node in memory:

$[http://jsfiddle.net/UuKU4/][js,result]

You should note that connecting a view to an element node in memory will still define an `el` property value. The idea is that `el` is always a node. You can set it to reference a node in the html page using a css selector or you can create an element node in memory by using the `tagname` property. If you don't provide an `el` value or `tagname` value when extending `Backbone.View` or instantiating a view the `el` value will default to an empty `<div>` (i.e. `tagname:'div'`).

**Notes:**

 - The `el` property, when connecting a view to an element in the browser DOM, accepts either a css selector string (i.e. `'#myView'`) or a jQuery object itself (i.e. `jQuery('myView')`) wrapping a node element in the HTML document loaded into the browser.

**2.8 - Setting Element Attributes On An Element Node In Memory**

The view options `id`, `className`, and `attributes` provide properties which add attribute values to the element node connect to a view [when the `tagname` property is used][9]. Below I use all three of these special properties to set attribute values on the node in memory that is connected to `myViewInstance`.

$[http://jsfiddle.net/sqAVL/][js,html,result]

**Notes:**

 - The special properties `id`, `className`, and `attributes` do not
   work on nodes that are already in the HTML DOM.

**2.9 - Using The jQuery-Wrapped `el` Shortcut (aka `this.$el`)**

Regardless if the element connect to a view (i.e. `el`) is in memory or in an the html page Backbone will setup a jQuery wrapper around the element so that you don't have to. It is basically a shortcut for creating your own wrapper around the element (i.e. `jQuery(this.el);`) that the view is connected too. In the code example below I verify that `this.$el` contains my `el` wrapped with jQuery methods.

$[http://jsfiddle.net/eFNPH/][js,html,result]

**2.10 - Using the jQuery-Wrapped `el` Scoped Shortcut (aka `this.$()`)**

Backbone provides [a short for performing jQuery functions scoped to the element the view is connected too][10]. This sounds complicated but using `this.$()` is simply a shortcut for not having to write `$(this.el).find()` or `this.$el.find()` every time you want to perform jQuery tasks on element nodes contained in the view. In the code below I make use of `this.$()` selecting the `<span>` contained inside of `myViewInstance`.

$[http://jsfiddle.net/VExa9/][js,html,result]

Notice that by using `this.$()` we are confining our jQuery selecting to the children element nodes of `el`. This is why the only `<span>` that is given the bold CSS styling is contained inside of the `el` (i.e. `<div id="myView"></div>`).

**2.11 - Setting Delegated Events For A Backbone View Using The `events` Property**

Backbone will wire up events for a view, which are [delegated][11] from the `el` node, from an `events` object that can be set when extending the `Backbone.View` or instantiating a view. In the code below I provide an events object when extending `Backbone.View` that adds a `click` and `mouseover` event to the `<button>` element in the view.

$[http://jsfiddle.net/y2EdC/][js,html,result]

The format for defining the event and the node inside of the view (i.e. inside of `el`) the event is attached, is as follows:

    // event: {'event selector' : 'callback function'}

    //Example based on previous JSFiddle below:

    'click button': 'sayHi'

    //notice the space divides the event from the selector

The event and the selector are the same values that jQuery normally takes as parameters when using the [jQuery().on()][12] method to do event delegation.

**Notes:**

 - If the selector is omitted, it is assumed by Backbone that the event should be bound directly to the `el` node. Behind the scenes this simply means the `jQuery.on()` is not passed a selector for event delegation.
 - The callback function associated with an event can either be a function value or the name of a view instance method as a string (e.g. `sayHi`).
 - Events are stored on view instances in an object called `events`.

**2.12 - Setting The Element Node Associated With A View Using `setElement()`**

Using the `setElement()` method available to view instances we can change the node element that is associated with the view. In the code below `myViewInstance` is initially associated to the `<div>` in the page with an id of "myView". Using the `setElement()` method the `myViewInstance` view is set so that the node element that is associated with the view is now the `<div>` with an id value of "anotherMyView".

$[http://jsfiddle.net/YDnAe/][js,html,result]

When using the `setElement()` method on a view, you should keep in mind that Backbone will remove any events on the previous element node and set them up on the new node you are moving the view too (i.e. `delegateEvents()` is called). In our code example above this would explain why the second "Say Hi!" button is functional while no longer responds to click events.

**2.13 - Removing A View From The DOM Using `remove()`**

The `Backbone.View` `remove()` method invokes the jQuery [remove()][13] method on the element node that is associated with the view. The reason that you'd pick this method over simply calling the jQuery method (i.e. `this.$el.remove();`) is that it will also call the `stopListening()` event on the view instance, removing all listeners set on the view.

In the code example below I set up the rendered view to remove itself if it is clicked. Clicking on the "invoke remove()" button will cause the button itself to be removed.

$[http://jsfiddle.net/RSuX7/][js,html,result]

**Notes:**

 - Remember the jQuery `remove()` event removes the element its call on,
   as well as everything inside, it.
 - Backbone relies on jQuery's `remove()` method which properly removes any events before any elements are removed.

**2.14 - Attaching Delegated Events Using `delegateEvents()`**

By default, Backbone will internally call `delegetEvents()` when a view is instantiated, referencing the `events` option object for any events that need to be setup. If, for whatever reason, the events attached to a view are removed, consider the `delegateEvents()` method the tool for refreshing/reattaching the events to the view.

To demonstrate the `delegateEvents()` method, in the code below, I render a view without setting up events during extending or instantiate. After the view is created, I update the views `events` property so that the `sayHi` callback is invoked when the `<button>` is clicked. Then I use the `delegateEvents()` method to attach any events in the events object to my view.

$[http://jsfiddle.net/WF2RU/][js,html,result]

**Notes:**

 - Calling `delegetEvents()` will remove all previous delegated event callbacks and then add them back. In a sense, a refresh.
 - To remove delegated events inside of views call the `undelegateEvents()` on the view instance.

**2.15 - Using Templates With Views**

Backbone does not force you into using a specific templating engine or pattern for rendering a view. However, a templating solution for rendering complicated bits of HTML from JSON data is provided by [underscore.js][14]. I am showing a small example of how templates in underscore.js (really lodash.underscore.js) can be used.

$[http://jsfiddle.net/6K7dm/][js,html,result]

Don't take the lack of in depth coverage on the topic of templates here to indicate the commonality of templates. Templates, in fact, are common if not almost always used when building Backbone applications. Really any web application! When rendering a view a templating engine should always be considered so that views do not become stuffed with DOM manipulation logic.

## 3 - Backbone.Model

**3.1 - Conceptual Overview Of A `Backbone.Model`**

A Backbone Model is likened to a table structure with column headers and rows of data. A `Backbone.Model` object defines the column labels and wraps the data (i.e. attributes) in each row with pre-defined and custom methods for data conversions, validations, and access control. Creating model instances from `Backbone.Model` or an extended `Backbone.Model`, provides the object to which the actual data is stored.  For example, a model for a contact in a contacts application might look something like the following: 

<table class="table table-bordered">
	<tr>
		<th>first name</th>
                <th>last name</th>
		<th>phone</th>
	</tr>
	<tr>
		<td>John</td>
                <td>Doe</td>
		<td>111-111-1111</td>
	</tr>
</table>

You can think of a `Backbone.Model` constructor as the column headers and the methods and properties common to each row of data. An instance created from the constructor is likened to populating the above table with actual data.

    //i.e.
    new Backbone.Model(
       {firstName:'John',lastName:'Doe',phone:'1-111-1111'}
    );

Keep in mind that out of the box `Backbone.Model` provides properties and methods (e.g. `get('phone')`) for operating on each row and facilitates the ability to define your own methods and properties (e.g. `getFullName()`)

What's been summarized thus far is only part of the nature of a Backbone model. In Backbone, the other part involves the logic for syncing data over HTTP through a restful JSON api using AJAX. In this article we are going to save the details pertaining to syncing for another time. This article will focus on the life cycle of a model (and collection of models) without clouding this objective with syncing.

**3.2 - Subclassing and Creating a `Backbone.Model`**

To create a generic model with data we only need instantiate an instance from `Backbone.Model` and pass in the values (or what Backbone calls `attributes`) the model will store. For example, below I create two contact models, one for John Doe and the other for Jane Doe.

    var contact1Model = new Backbone.Model(
        {firstName:'John',lastName:'Doe',phone:'111-111-1111'}
    );

    var contact2Model = new Backbone.Model(
        {firstName:'Jane',lastName:'Doe',phone:'222-222-2222'}
    );

However, it's more likely that you will be extending/subclassing the base `Backbone.Model` before you instantiate an instance so that you can add your own domain specific properties.

Below I extend the `Backbone.Model` constructor, creating a sub-constructor, (i.e. `ContactModel`) and define the `getFullName()` method so all models created from `ContactModel` inherit the `getFullName()` method.

$[http://jsfiddle.net/5gfv5/][js,result]

Notes:

 - An array of data can be passed in place of an object when
   instantiating a model. When this is done Backbone will convert the
   array to an object by giving numeric property names to the values
   passed (i.e. `['foo','bar']` becomes `{'1':'foo','2','bar'}`).
 - The second argument that can be passed when instantiating a model, is
   an options object containing the options `collection`, `url`,
   `urlRoot`, and `parse` options. Most of these will be addressed in
   the next article given that they are related to syncing.
 - Don't forget about the `initialize` function, which can be set up when
   extending models. This is an ideal place to house logic that needs to
   run when model instances are created. Especially considering that the
   `this` value inside of the initialize function is scope to the
   instance created.
 - A convenient `clone()` method is available on model instances that   
   will return a new instance of a model with a copy of the data the   
   `clone()` method is called on. It is a handy way of creating a model
   from a model instance.

**3.3 - `Backbone.Model` Methods, Properties, And Events**

Backbone model instances have the following [methods and properties][15]:

* get
* set
* escape
* has
* unset
* clear
* id
* idAttribute
* cid
* attributes
* changed
* defaults
* toJSON
* **sync**
* **fetch**
* **save**
* destroy
* Underscore Methods:
  * keys
  * values
  * pairs
  * invert
  * pick
  * omit
* validate
* validationError
* isValid
* **url**
* **urlRoot**
* **parse**
* clone
* **isNew**
* **hasChanged**
* changedAttributes
* previous
* previousAttributes

The methods and properties in **bold** will not be discussed in this article. In the next article we will deal with the methods and properties in bold that pertain to syncing.

Additionally, models can make use of the following built-in events:

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

**3.4 - Setting Default Model Values/Attributes**

When extending a model, you can provide a set of default values that are shared among all instances. In the code below I setup a default value for `firstName` and `lastName`.

$[http://jsfiddle.net/8TQQR/][js,result]

If you would prefer that instances not reference the same default object, you can provide a function value for defaults that will create unique default values (i.e. not referencing that same default object).

$[http://jsfiddle.net/WzpwE/][js,result]

In the code example above, each instance will now make use of a unique `defaults` object, instead of each instance referencing the same `defaults` object.

**3.5 - Setting, Verifying, Getting, UnSetting, and Clearing Model Data**

Backbone provides a generic set of methods (`set()`, `has()`, `get()`, `unset()`, `clear()`) for working with model data. Below I demonstrate each of these methods.

$[http://jsfiddle.net/Xh37w/][js,result]

Notes: 

 - These methods should be used instead of directly editing the internal
   `attributes` object so the built in events on models can be
   triggered appropriately.
 - The `set()`, `unset()`, and `clear()` methods invoke the internal `change` event.
 - The `set()` method accepts an options object (e.g.
   `set({'name':'value','name':'value'})`) or two string parameters
   (e.g. `set('name','value')`).
 - The `set()`, `unset()`, and `clear()` methods all accept the `{silent: true}` option which stops any Backbone built-in event form firing.
 - The option `{validate:true}` can be used with the `set()`
   method to validate any values before they are set (e.g. `set({'foo':'foo'},{validate:true})`).

**3.6 - Accessing A Models Data/Attributes**

Backbone provides the `attributes` property which gives direct access to the internal object containing a models data.

$[http://jsfiddle.net/cWujE/][js,result]

While Backbone provides direct access to this object, it is not common for this object to be manipulated directly. In fact, don't do it. If you want to edit model data use `set()`, `get()`, `unset()`, `clear()` or make a copy (e.g. `model.toJSON();`) of the `attributes` object, edit that, then update the model using `set()`.

**Notes:**

 - Direct Manipulation of the `attributes` property will not invoke internal model events.

**3.7 - Getting A Copy Of A Models Data/Attributes Using `toJSON()`**

The `toJSON()` model method returns a copy of the `attributes` internal data object.

$[http://jsfiddle.net/7cn5G/][js,result]

This can be handy when you need a clean copy of the object for a template.

**Notes:**

 - The `toJSON()` method makes a shallow copy (i.e. [_.clone][16]) of the attributes object. Any nested object or arrays will be referenced from original, not copied.
 - Passing a model instance to `JSON.stringify` will internally use the Backbone `toJSON()` method.

**3.8 - Filtering Model Data**

Backbone proxies 6 methods (`keys()`, `values()`, `pairs()`, `invert()`, `pick()`, `omit()`) from the underscore.js library to be used directly on model data. These functions provide aid when filtered states of model data are needed. Below I demonstrate the four most used methods.

$[http://jsfiddle.net/kXKTb/][js,result]

**3.9 - Listening To Model `change` Events**

Anytime a change occurs with a model's data a `change` event is broadcasted so that typically a view can listen for such an event.

In the following code example, a model is created and then a view is created to listen for changes on the model using the `change` event. Any changes to the model data will trigger a `change` event and then ideally a re-render.

$[http://jsfiddle.net/baMYn/][js,result]

It is possible to listen only for a specific attribute change in a model by indicating the name of the attribute along with the change event when the event listener is setup (i.e. `'change:[ATTRIBUTE]'`). For example, in the code below, I have changed the previous code example to only listen for changes on the `firstName` attribute in the contact model.

$[http://jsfiddle.net/xBkBY/][js,result]

**3.10 - Verify if a Model Has Changed And What Changed Using `hasChanged()` and `changedAttributes()`**

The `hasChanged()` and `changedAttributes()` model methods provide the ability to determine if something has changes since that last `change` event and specifically what changed.

In the following code, I set up a contact model and then immediately changed the phone number attribute. 

$[http://jsfiddle.net/R8n89/][js,result]

By changing the phone number in the above code example, the internal `change` event is called for the first time and the `hasChanged()` method will return a boolean indicating that the model has changed. To get an object containing the attributes that have change invoke the `changedAttibutes()` method on the model.

**3.11 - Getting Previous Attribute Values Using `previous()` and `previousAttributes()`**

The `previous()` and `previousAttributes()` method can be used to get the value of an attribute prior to the last `change` event. In the following code example, I set up a contact model, change the `phone` and `firstName` attributes and then use the `previous()` and `previousAttributes()` method to retrieve the prior value for the phone attribute and the state of all mode attributes before the last change event.

$[http://jsfiddle.net/jyLzH/][js,result]

**3.12 - Validating Model Data Before Its Set or Saved**

When setting or saving model data (we will talk about saving in the next article) a validation function can be invoked on the data validating its quality before anything is set or saved.

By default, the `validate` property is left undefined. To define a validate function on a model pass `validate:function(attributes,options){}` to `extend()` or directly update the `validate` property on a model instance. To trip a validation error simply return any value from the function except `false`.

In the code below, I set up a validation function for phone numbers and make sure any invalid phones numbers can not be `set()`.

$[http://jsfiddle.net/w5ug9/][js,result]

The last value returned from the validation function that is not false, can be referenced from the models `validationError` property. In the code above we return an invalid message from the validation function and then accessed the `validationError` property to log this message.

**Notes:**

 - A `validate` callback function is sent all of the models attributes
   as the first parameter, and the second parameter contains the options
   object from the `set()` or `save()` methods that trip the validation.
 - Validation only occurs when using `save()`, the `set()` method and the
   `validation:true` option, or by manually running validation using
   `isValid()`.

**3.13 - Listening To A Model's `invalid` Event**

The built in `invalid` event will be broadcasted when a model's `validate` function fails. In the code below I demonstrate listening for this event and then log the parameters passed to the invalid callback function.

$[http://jsfiddle.net/3XkY4/][js,result]

**3.14 - Manually Run `validate` On Model**

The `validate` function can be manually invoked by calling the `isValid()` method. In the code example below the `validate` function is set up when extending the `Backbone.model` constructor so that all model instances can make use of the validate function. To make sure that data is always validated, I am running the `validate` function when the `initialize` function is called to catch any invalid data that is passed during the creation of the model instance.

$[http://jsfiddle.net/NKnQ8/][js,result]

Notice that the `isValid()` method returns a helpful boolean indicating if the model is valid or not.

**Notes:**

 - The `save()` and `set()` internally make use of the
   validate function.


## 4 - Backbone.Collection

**4.1 - Conceptual Overview Of A `Backbone.Collection`**

A Backbone collection represents a logical grouping of models and provides methods and properties for working with (grouping, sorting, filtering) these groups of models. To finalize the contact illustration I have been using in this article (i.e. a model = row of labeled data for a contact in a table), you can think of a collection as the entire table that contains rows of contact data. Below I have updated the table containing contact details discussed at the start of the model section so that the entire table can be considered a contacts table.

<table class="table table-bordered">
<tr>
		<th colspan="3" align="center">Contacts:</th>
    </tr>
	<tr>
		<th>first name</th>
                <th>last name</th>
		<th>phone</th>
	</tr>
	<tr>
		<td>John</td>
                <td>Doe</td>
		<td>111-111-1111</td>
	</tr>
</table>

Of course, a `Backbone.Collection` is much more than a label like the previous table might express. Collections can capture events (change, destroy, request, sync, error, invalid) that are triggered on its models and collections also have their own set of useful events (e.g. add, remove, reset, sort, request, sync).

**Notes:**

 - In its simplest form, a collection is simply an array of references
   to model instances. In fact, each collection instance provides a
   `models` property which is a direct reference to the internal array
   that contains the model instances.

**4.2 - Creating a `Backbone.Collection` From Model Instances**

To create a generic collection we only need instantiate an instance from `Backbone.Collection` and pass in during instantiation an array of model instances. In the code example I take the contact models we have been working with in this article and pass them as the first option when creating a collection.

$[http://jsfiddle.net/yNwU2/][js,result]

Notes:

 - The first parameter passed when instantiating a collection can be a
   model instance or an array of model instances. However, if the
   collection being created has a reference (via `model` property) to a
   model constructor, the collection can internally instantiate models by
   passing a raw attribute/data object or an array of raw
   attributes/data objects.
 - The second parameter passed to a collection constructor is an options
   object configuring the `url`, `model`, and `comparator` options.
 - Just like `Backbone.View` and `Backbone.Model` a `Backbone.Collection` can be extended (i.e. subclassed) using `extend()` and defining an `initialize` function when extending will be invoked when a collection is created.
 - A `clone()` method is provided for creating collection objects by
   cloning a collection instance. When `clone()` is used a new
   collection instance is returned containing the same models.
 - A `length` property, available on collection instances, maintains the
   number of models it contains.
 - When a model is added to a collection, the model's `collection`
   property is  automatically update to contain a reference to the
   collection.

**4.3 - Creating a `Backbone.Collection` From A Model Constructor**

A common pattern for creating collections is to point the collection `model` property at a model constructor and then instead of passing an array of references to model instances, the collection itself can instantiate models from raw data and store these instances in the collection. In the code below an array of raw data and a model constructor (i.e. `ContactModel`) is passed to the `Backbone.Collection` constructor when instantiating a collection.

$[http://jsfiddle.net/JQd4T/][js,result]

The results are exactly the same as passing in a reference(s) to models already created, but by using the `model` property the collections can do some of the setup for us.

Notes:

 - The `model` property can also be set when sub-classing the
   `Backbone.Collection` constructor (e.g. `ContactsCollection = Backbone.Collections.extend({model:modelConstructor})`
 - The `model` property can either point to a model constructor or be a
   function value that returns a model instance

**4.4 - `Backbone.Collection` Methods, Properties, And Events**

Backbone collection instances have the following [methods and properties][17]:

* model
* models
* toJSON
* **sync**
* Underscore Methods (28)
* add
* remove
* reset
* set
* get
* at
* push
* pop
* unshift
* shift
* slice
* length
* comparator
* sort
* pluck
* where
* findWhere
* **url**
* **parse**
* clone
* **fetch**
* **create**

The methods and properties in bold will not be discussed in this article. In the next article we will deal with the methods and properties in bold that pertain to syncing.

Additionally, collections can make use of the following built-in events:

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

**Notes:**

 - Because collections contain models, any event that is triggered on a
   model will also be triggered on the collection. This means you can
   listen for the model `change`, `change[:attribute]`, `destroy`,
   `error`, and `invalid` event on a collection (e.g. `contacts.on('change',function(model,options){}`).

**4.5 - Getting All Model Data/Attributes Out Of A Collection Using `toJSON()`**

The collection `toJSON()` method will return an array containing a copy of the internal attributes for each model in a collection. In the code below I log an array that contains all of the attributes for each model in the collection using `toJSON()`.

$[http://jsfiddle.net/5v8fc/][js,result]

**4.6 - Getting Models From A Collection Using `models`, `get()`, `at()`, `where()`, and `findWhere()`**

Direct access to the array that contains all the model references for a collection is provided by the`models` property. We have used this property already in this section, but to demonstrate its value, examine the code example below which extracts the array of model instances (i.e. model objects) from a collection using the `models` property.

$[http://jsfiddle.net/wJdSz/][js,result]
 
The `where()` method is provided so that the `models` array can be filtered by attributes. In the code example below I use `where()` to get an array of only the models in the collection that have an attribute of `{firstName:'Doe'}`; 

$[http://jsfiddle.net/wxkaD/][js,result]

Using the `get()`, `at()`, and `findWhere()` collection methods we can get a single model reference instead of an array of model references. These three methods are demonstrated in the code below.

$[http://jsfiddle.net/hNVMD/][js,result]

**Notes:**

 - Manipulating a collections module by making direct changes to the
   `models` array should be avoided given that doing so will not trigger
   events such as `add` or `remove`. Its better to use 
 - The `findWhere()` will return the first match found in the
   collection.
 - `at()` will honor sorting order done on collections or return the model
   based on insertion order
 - Collection's have a `slice()` method that [is a shortcut][18] for simply
   doing `models.slice(begin,end)`.

**4.7 - Sorting A Collection**

By default a collection does not sort itself. Models are simply in the order that you add them. To have a collection keep a sort order the `comparator` property should be defined as a [sortBy][19] function taking a single argument, as a [sort][20] function taking two arguments, or as a string indicating the attribute to sort by. In the code example below we pass the comparator value during collection creation so that the collection will keep models sorted by `firstName`.

$[http://jsfiddle.net/kCWnt/][js,result]

**Notes:**

 - Changes to a collection (i.e. `add()`) will automatically invoke the
   sorting logic defined by `comparator`. However, if the `attributes`
   in a model change, you will have to manually sort the collection using
   the `sort()` method to keep the collection updated and correctly
   sorted.
 - Calling `sort()` triggers a `sort` event on collections.
 - The comparator value can be set after the creation of a collection by
   defining a sort value (i.e. sort, or sortBy function, or attribute to sort by) for the collection's `comparator` value.

**4.8 - Getting an Array of Model Attributes And Values From Each Model in a Collection Using `Pluck()`**

The `pluck()` method is extremely handy for creating an array of all of the values for a specific attribute in a collection of models. For example, in the code below I set up a collection with three models, that all contain a `firstName` attribute. To extract the `firstName` value for each model I call the `pluck('firstName')` method on the collection.

$[http://jsfiddle.net/Q9Nk5/][js,result]

**4.9 - Adding Models To A Collection Using `add()`, `push()`, `unshift()`**

The `add()` method for adding models to a collection can add a single instance of a model or an array of model instances. If the collection has a value for its `model` property, you can pass raw data/attribute objects or an array of raw data attribute objects. In the code example below I demonstrate all of these methods for adding a model to a collection.

$[http://jsfiddle.net/9K4SF/][js,result]

The `push()` and `unshift()` methods function identically to the `add()` method, and taking the same parameters, except that these methods will either add the model(s) to the end (i.e. `push()`) of the collection or to the beginning (i.e. `unshift()`) of the collection.

**Notes:**

 - Passing the `{at: index}` option to `add()` as the second parameter will splice the added model(s) into the collection at a specific index.
 - Adding a model to a collection twice (i.e. same model) will basically do nothing. The model is ignored, unless you pass the `{merge:true}` option as the second parameter to the `add()` method. If a merge occurs the `change` event will be triggered on the model and collection.
 - The `add` event is triggered on a collection when a model is added, pushed, or shifted to the collection.

**4.10 - Removing Models From A Collection Using `remove()`, `pop()`, `shift()`**

The `remove()` method can be used to remove a model(s) from a collection by passing references to the models that need to be removed. Below I demonstrate the removal of a single model as well as a group of models from a collection.

$[http://jsfiddle.net/HEpUA/][js,result]

The `pop()` and `shift()` are shortcut methods for removing models. Calling `pop()` will remove the last model and `shift()` will remove the first model.

**Notes:**

 - Calling `remove()` with no parameters will **not** remove all models. To remove all models from a collection call the `reset()`, passing it no arguments. (*HUH?*) 
 - A `remove` event is triggered on the collection when the `remove()`,
   `pop()`, or `shift()` methods are invoked.

**4.11 - Add, Merge, and Remove Models At The Same Time Using `set()`**

The `set()` method will update the state of the models in a collection by attempting to match the changes indicated by the array of model instances passed to the `set()` method. In other words, using `set()` will attempt to synchronize the array of models passed in with the internal state of the models in the collection by intelligently deciphering the differences and adding, removing, and merging according to the differences.

In the code example below I set up a collection and then using `set()` I add to the collection, remove a model from the collection, and update a model in the collection all using `set()`.

$[http://jsfiddle.net/FMEuj/][js,result]

Notes:

 - The `add`, `remove`, and `change` events are all triggered when using `set()` on the model and the collection objects.
 - To disable the add, remove, or merge functionality of `set()` you can pass it the options `{add: false}`, `{remove: false}`, or `{merge: false}`.

**4.12 - Replacing All Models In (i.e. bulk replace/reset) A Collection Using `reset()`**

The `reset()` method can be used to replace the models in a collection with a new set of model(s). It is essentially like removing all model(s) in a collection and adding a new model(s).

**Notes:**

 - Calling `reset()` with no arguments will remove all of the models from the collection.
 - When the `reset()` method is used a `reset` event is triggered.

**4.13 - Using underscore.js (or lodash.js) Methods On Models (i.e. `models`) In A Collection**

A collection instance has access to many useful methods for operating on the array of models the collection instance contains. But you should be aware that [Backbone mixes into to the prototype chain of a collection][21] the following underscore.js (or lodash.js) methods which can be useful as well. (e.g. `myCollectionInstance.first()` same as `myCollectionInstance.at(0)`).

 - forEach or each
 - map or collect
 - reduce or foldl or inject)
 - reduceRight or foldr
 - find  or detect
 - filter or select
 - reject
 - every or all
 - some or any
 - contains or include
 - invoke
 - max
 - min
 - sortBy
 - groupBy
 - sortedIndex
 - shuffle
 - toArray
 - size
 - first or head or take
 - initial
 - rest or tail
 - last
 - without
 - indexOf
 - lastIndexOf
 - isEmpty
 - chain

In the code example below I make use of the `each()` and `first()`.

$[http://jsfiddle.net/5C5W9/][js,result]

## 5 - Building A Simple Contacts Application

**5.1 - Overview**

Imagine for a moment that you are in your favorite spreadsheet tool. You create a spreadsheet called "contacts" (i.e. a collection). Next you define column headers in the spreadsheet, so you add the headers "first name", "last name" and "phone" (i.e. a model). Basically what you have is a spreadsheet housing contact data (i.e. {firstName:'Jane, lastName:'Doe', phone:'111-111-1111'}). Now, imagine you want to use this information from the spreadsheet to create a UI (i.e. a view) to view/add/delete contacts. Backbone can do that! Let's do it.

**5.2 - Extend `Backbone.Model` creating `Contact` Constructor/Class**

    var Contact = Backbone.Model.extend({
        defaults: {
            firstName: null,
            lastName: null,
            phone: null
        },
        getFullName: function(){
            return this.get('firstName') +' '+ this.get('lastName');
        }
    });

**5.3 - Instantiate `contacts` Collection, Passing A Model Constructor/Class and A Contact**

    var contacts = new Backbone.Collection({ //seed it with some data
        firstName: 'Jane',
        lastName: 'Doe',
        phone: '111-111-1111'
    }, {
        model: Contact
    });

**5.4 - Extend `Backbone.View` creating `AddContactsView` & Instantiate An Instance**

    var ContactListView = Backbone.View.extend({
        el: '#contacts',
        events: {
            'click li button': 'removeContact'
        },
        initialize: function () {
            this.render(); //render list
            this.listenTo(contacts, 'add remove', this.render); //the magic
        },
        removeContact: function (e) {
            $(e.target).parent('li').remove();
            contacts.findWhere({
                firstName: $(e.target).parent('li').find('span').text().split(' ')[0].trim(),
                lastName: $(e.target).parent('li').find('span').text().split(' ')[1].trim()
            }).destroy(); //this will invoke the internal 'remove' event
        },
        render: function () {
            if (contacts.length > 0) {
                this.$el.empty();
                contacts.each(function (contact) {
                    this.$el.append('<li><span>' + contact.getFullName() + '</span>'+' / '+ contact.get('phone') + '<button type="button" class="btn-xs btn-danger removeContactBtn">X</button></li>');
                }, this);
            }
        }
    });
    var contactListViewInstance = new ContactListView();

**5.5 - Extend `Backbone.View` creating `ContactListView` & Instantiate An Instance**

    var AddContactsView = Backbone.View.extend({
        el: 'fieldset',
        events: {
            'click button': 'addContact'
        },
        addContact: function () {
            var firstName = this.$('#firstName').val();
            var lastName = this.$('#lastName').val();
            var phone = this.$('#phone').val();
            if (firstName && lastName && phone) {
                contacts.add({ //this will invoke the Backbone internal 'add' event
                    firstName: firstName,
                    lastName: lastName,
                    phone: phone
                });
                this.$('input').val('');
            }
        }
    });
    var addContactsViewInstance = new AddContactsView();

**5.6 - Working Demo**

Below is a working demo of the contacts application discussed in this section. Examine the application code until a complete understanding of the role and function each part (i.e. model, collection, views) plays in the creation of the application. Refer back to section 2, 3, or 4 of this article as needed.

$[http://jsfiddle.net/Qwb9r/][result,js,css,html]

## 6 - Conclusion

It was the intention of this article to give the reader a thorough understanding of a Backbone view, model, and collection. In the next part of this article we'll examine [syncing][22] and the methods associated with syncing models and collections to a backend.


  [1]: http://tech.pro/tutorial/1367/part-1-backbonejs-deconstructed
  [2]: http://tech.pro/tutorial/1367/part-1-backbonejs-deconstructed#constructing_backbone_objects
  [3]: http://jsfiddle.net/codylindley/Qwb9r/
  [4]: http://jsfiddle.net/Qwb9r/
  [5]: http://backbonejs.org/docs/backbone.html#section-120
  [6]: http://backbonejs.org/#View
  [7]: http://backbonejs.org/#View-render
  [8]: http://domenlightenment.com/#3
  [9]: http://backbonejs.org/docs/backbone.html#section-133
  [10]: http://backbonejs.org/docs/backbone.html#section-125
  [11]: http://domenlightenment.com/#11.14
  [12]: http://api.jquery.com/on/
  [13]: http://api.jquery.com/remove/
  [14]: http://underscorejs.org/#template
  [15]: http://backbonejs.org/#View
  [16]: http://underscorejs.org/#clone
  [17]: http://backbonejs.org/#View
  [18]: http://backbonejs.org/docs/backbone.html#section-98
  [19]: http://underscorejs.org/#sortBy
  [20]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort?redirectlocale=en-US&redirectslug=JavaScript/Reference/Global_Objects/Array/sort
  [21]: http://backbonejs.org/docs/backbone.html#section-116
  [22]: http://backbonejs.org/#Sync