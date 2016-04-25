---
date: 2014-12-29T22:41:00Z
tags: ["Technology", "Web Components", "Front End Development"]
title: "Web Components"
---

One of my current interests is a new specification for encapsulating presentation layer elements called [Web Components](http://webcomponents.org/). I truly believe that Web Components will change how we think about modularity on the web.  One of the core tenets of object-oriented programming is encapsulation and promoting software reuse. We've been doing this for years on the backend, but until recently, it was difficult to encapsulate HTML, CSS, and Javascript at the presentation layer. Web Components naturally build on HTML elements in a way that promotes modularity and reuse, making code more readable, and enabling developers and designers to work faster. With this goal in mind, I believe that Web Components will create value that exceeds the sum of the effort spent on developing them.

Web Components rely on four primary standards:

* [Custom Elements](http://w3c.github.io/webcomponents/spec/custom/): This specification describes the method for enabling the element author to define and use new types of DOM elements in a document.
* [HTML Imports](http://w3c.github.io/webcomponents/spec/imports/): HTML Imports are a way to include and reuse HTML documents in other HTML documents.
* [HTML Templates](https://html.spec.whatwg.org/multipage/scripting.html#the-template-element): The template element is used to declare fragments of HTML that can be cloned and inserted in the document by script.
* [Shadow DOM](http://w3c.github.io/webcomponents/spec/shadow/): This specification describes a method of combining multiple DOM trees into one hierarchy and how these trees interact with each other within a document, thus enabling better composition of the DOM.

### What do we mean by encapsulation?

One of the coolest parts of the W3C draft for Web Components is the specification for [Custom Elements](http://w3c.github.io/webcomponents/spec/custom/), which describes how we can define new types of DOM elements and use them in our web sites. This means that we can create our own HTML tags!

Without Web Components, if you wanted to embed a Google Map onto your website, you'd probably use an iframe:

{{< highlight html >}}
<iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d50710.53534045161!2d-122.08126699999998!3d37.403819399999996!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x808fb7495bec0189%3A0x7c17d44a466baf9b!2sMountain+View%2C+CA!5e0!3m2!1sen!2sus!4v1419821604530" width="600" height="450" frameborder="0" style="border:0"></iframe>
{{< / highlight >}}

That's pretty ugly. While this code makes sense to a web developer, the relationship between the syntax and the semantics is unclear. Locally, the **iframe** creates a virtual window that is 600 pixels wide and 450 pixels high and fills it with content provided by the URL. However, using the [google-map](http://googlewebcomponents.github.io/google-map/components/google-map/) Custom Element, we can define the **google-map** tag as an HTML element that represents a Google Map:

{{< highlight html >}}
<google-map latitude="37.779" longitude="-122.3892"></google-map>
{{< / highlight >}}

This code is much more readable. The **google-map** element encapsulates a Google Map as part of the DOM with attributes such as latitude and longitude. It also encapsulates the formatting (style) and the interaction with the Google Maps API, allowing designers and developers to create content faster.

### How do we use a Custom Element?

Obviously, you can't simply use the **google-map** tag without first defining it as a Custom Element. The Web Components specifications are still in draft status, therefore not all browsers natively support them. If your web browser does not support Web Components, then Custom Elements won't render, but the [webcomponents.js](http://webcomponents.org/polyfills/) library provides polyfills for these web browsers to encourage adoption of these specifications. Additionally, there are libraries, such as Google's [Polymer](https://www.polymer-project.org/), that extend these polyfills. Polymer provides a thin API on top of web components, which makes it really easy to create your own web components. You'll inevitably hear this magic referred to as *syntactic sugar*.

Once you have a web browser that supports Web Components (or have the webcomponents.js polyfills loaded), you can import an existing Custom Element and begin using it. There are already websites like [CustomElements.io](http://customelements.io/) that provide access to a growing collection of existing web components that you can import and begin using.

In the following code sample, we import the **google-map** Custom Element definition from *google-map.html* and can begin using the newly defined element. This snippet assumes that the *google-map.html* exists as a web component in the same directory as the current page of your web application.

{{< highlight html >}}
<!-- Load Polyfill Web Components support for older browsers -->
<script src="components/webcomponentsjs/webcomponents.js"></script>
<!-- Import element -->
<link rel="import" href="google-map.html">
<!-- Use element -->
<google-map lat="37.790" long="-122.390"></google-map>
{{< / highlight >}}

That's it! In the next section, we'll dive deeper into the internals of Web Components.

### How do I make my own Custom Elements?

So far, we've seen the **google-map** Custom Element in action and we've also seen how the [HTML Import](http://w3c.github.io/webcomponents/spec/imports/) specification allows us to import a Custom Element that someone else has written, but we haven't looked at the specifications that make all this magic happen. Behind the scenes, the HTML **template** element and the **Shadow DOM** specifications enable the encapsulation of the HTML, CSS, and Javascript into a nicely bundled element.

The **template** element is part of the WHATWG HTML living standard and describes a method to "declare fragments of HTML that can be cloned and inserted in the document by script". Custom Elements use the **template** element to define the inert document fragment that will be cloned into the DOM each time it is instantiated. This brings up an interesting point regarding the semantic meaning of the template fragment.  By itself, the **template** element doesn't actually represent anything.  Take the following snippet:

{{< highlight html >}}
<template>
  <div> Hello </div>
</template>
{{< / highlight >}}

This **template** element contains a DocumentFragment consisting of a single **div** wrapped around the text "Hello", but this doesn't actually represent anything... yet. Once the template is cloned, the templated HTML fragment is copied underneath the newly instantiated node et voila! Custom Elements rely on templates to clone and instantiate new DOM elements with HTML fragments, but they are more powerful because they *are* HTML elements and therefore, they have attributes and can be treated like any other element in the DOM.  Take that, iframe content!

The other specification that enables us to create Custom Elements is the [Shadow DOM](http://w3c.github.io/webcomponents/spec/shadow/). If you're not standing in an empty auditorium or over a large chasm, now is the time to find one so that you can bellow "SHADOW DOM!" using your most ominous cackle.  The Shadow DOM sounds cool because it *is* really cool. It describes how multiple DOM trees can be combined into one hierarchy and how these trees interact with each other. What this allows us to do is conceptually maintain a functional boundary between the DOM in your web application and the DOM in your web component. This is important because DOM selectors or CSS styles could otherwise interfere with each other and cause interesting side effects in the Web Components used by your application. The Shadow DOM is a critical specification that enables Web Components to be fully encapsulated. Web Components could not exist without the Shadow DOM.

### Getting Started

If you want to start creating your own Web Components, check out [WebComponents.org](http://webcomponents.org/) and download one of the polyfill libraries.

Alternatively, you can fork an existing boilerplate project from GitHub:

 * [Google Polymer Boilerplate](https://github.com/webcomponents/polymer-boilerplate)
 * [Mozilla X-Tag Boilerplate](https://github.com/webcomponents/x-tag-boilerplate)
 * [Vanilla JS Boilerplate](https://github.com/webcomponents/x-tag-boilerplate)
