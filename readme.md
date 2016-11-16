<!---
title: AJAX with JS & JQuery
type: Lesson
duration: "1:20"
creator:
    name: Micah Rich
    city: LA
competencies: Front-end intro
--->

<!--11:00 15 minutes -->

<!--Hook: So now we know how to communicate with an API using Postman.  However, we have all these awesome tools with Javascript that we could use to manipulate, save, and display this data.  It's too bad there's no way for Javascript to interact with web APIs... *psyche!*  The way to do that is called AJAX, and we're going to talk about it today.  -->

# AJAX with JS & JQuery

### Objectives

- **Implement** an AJAX request with Vanilla JS
- **Implement** a jQuery AJAX client for a simple REST service
- **Reiterate** the benefits of separation of concerns – API vs. Client

### Preparation

- **Understand** basics of JS
- **Understand** what jQuery is and how to use it for DOM manipulation
- **Understand** HTTP verbs & the concept of RESTful JSON APIs

## What is AJAX? An Introduction

AJAX (Asynchronous JavaScript and XML) is a method of building interactive applications for the Web that processes user requests immediately, without re-rendering a whole page.

> **Example:** A weather forecasting site could display local conditions on one side of the page as soon as a user finishes typing in a zip code. The temperature could also refresh every minute, without the user having to hit a refresh button.

In general the process looks like this – use JavaScript on the client side to hit an API (without reloading a page), then use the pure data you get back to manipulate the DOM somehow if you need to. This DOM manipulation can take the form of rendering a template or even something as simple as changing a number on the page.

### Advantages

- __Faster__ - This is the most obvious reason for using to AJAX on your front-end: AJAX allows easier and quicker interaction between user and website as pages are not reloaded for content to be displayed.  The server doesn't have to get data, render HTML, and then spit it out, it just has to get data and your already-loaded front-end does the rest.

- __Compact__ - With AJAX, several application features can be handled using a single web page. That means we modularize our app into smaller bits, and it becomes easier to work on.

- __Backend Separated from Front-end__ - Applications that use AJAX-heavy front-ends allow developers to avoid working on both sides of the stack at the same time. Some developers can be dedicated to building an API that just serves data, and others can focus on consuming that data and building interfaces.


### Disadvantages

- __The back and refresh button are rendered useless__ - Since things are loaded dynamically on a page, without that page reloading (or more importantly a URL being changed), clicking the back or refresh button won't work the way you're used to. That's actually a pretty big deal – UX designers are very familiar with the fact that users are _accustomed_ to being able to hit back when they need to. Some advanced front-end frameworks have tried to solve this issue with clever workarounds, but that's not always clean and not always accurate.

- __Javascript can be disabled__ - While Javascript is secure and has been heavily used by websites for a long period of time, a percentage of website surfers prefer to turn Javascript functionality off on their browser, rendering the AJAX application totally useless. That's not always the best thing to design for, and more often than not, you'll find yourself assuming users have JS on, but it's important to know your whole site could be useless in some situations.

- __You have to consider the UX even more__ - While UX is crucial for _any_ application, the fact that a page doesn't refresh means you have to be even more considerate of what a user is experiencing. If something in your Javascript goes wrong, your AJAX breaks, and you don't have failsafes thoughtfully built in, your user might be clicking a button and seeing absolutely nothing happen. Most common users won't have their consoles open to notice any errors.

### Why are we learning it?
As you're learning how to build APIs on the server side, you need to start learning how to consume your APIs on the client side.

While we're going to be tackling some advanced front-end frameworks in the next unit, you, as a junior full-stack developer, need to be able to do something awesome with the APIs you're learning to make. So we're going to tackle the basics and build on them even further, later.

<!--11:15 5 minutes -->

## Setup - Codealong

While we're still learning the ins-and-outs of building APIs, let's use an already-made API for today. We'll use one that works a lot like Mongo and Express (coming soon!) and comes with RESTful resources out of the box.

This handy thing lives at https://api.doughnuts.ga/doughnuts – it's a simple dummy data service that'll let us do GETs & POSTs quickly. You can quickly check out https://www.doughnuts.ga/ to see what it's about.

While you're at it, in the starter-code folder, we've got a super basic index and a CSS file to get started. Nothing fancy.

Open up your `index.html` in a browser, and you'll see:

<img width="752" src="https://cloud.githubusercontent.com/assets/25366/9149100/765ffaae-3d4d-11e5-850d-c8b15c7900e6.png">

Make sure to open your console - we're going to be working with it quite a bit.

Now, we've set a few things up for you. Besides the beautiful layout, we have a form with an input and a select dropdown. We've also already included jQuery, though we won't use that for the first few minutes.

<!--11:20 15 minutes -->

## GET Requests - Catch Up

#### Vanilla JavaScript

Open up your browser and the console and follow along.   We're gonna start with old-school JavaScript, so you can see how it works without any libraries.

_**Note:** You might want to write the code out in Sublime first and then copy it across to your console as it is easier to change typos._

```js
  var request = new XMLHttpRequest();
  request.open('GET', 'https://api.doughnuts.ga/doughnuts', true);

  request.onload = function() {
    if (request.status >= 200 && request.status < 400) {
      // Success!
      var resp = request.responseText;
      console.log(resp);
    } else {
      // We reached our target server, but it returned an error
      console.log('Uh oh, an error on the server side');
    }
  };

  request.onerror = function() {
    // There was a connection error of some sort
    console.log('Something went wrong with the client side.');
  };

  request.send();
```

Let's walk through this for a second. AJAX uses HTTP to request XML (or JSON), so we're going to open a new request and tell it which verb we need and the URL.

According to the documentation, `request.open` takes 3 arguments - `method`,`url`, and `async`.

Method matches our HTTP verb, URL is the path we are hitting, and async is just asking if it should perform the function asynchronously - by now, we have a basic idea about what that means.

Then, of course, we've got a function that runs if the requests works, and another if it doesn't. Just `console.log` for now.

Finally, we send our request and see what happens. In this instance, we get back an array of doughnuts.

<!--11:35 15 minutes -->

#### jQuery GET Requests

While it's good to have seen the straight old-school Vanilla JS way of doing it, we've got a lot of libraries these days that are designed to get you there faster - jQuery is one of those libraries.

Since we've already included jQuery in our HTML's head, let's try doing the same AJAX GET request with jQuery, together.

If you hit `cmd+k` in your browser console, it'll clear it to start fresh.

```js
  var ajax = $.get('https://api.doughnuts.ga/doughnuts')
```

Let's look at what our `ajax` variable holds now.

<img width="633" src="https://cloud.githubusercontent.com/assets/25366/9149156/825a0392-3d50-11e5-80c7-de1088a5b84e.png">

That's some awesome info. What's this `responseJSON`? Looks useful:

```js
  ajax.responseJSON
```

Well isn't that just exactly what we need? How handy!

What did this do? Without refreshing the page, we hit an external API – a totally different URL that's rendering JSON data instead of views – and brought that data back into our page.

<!-- Show what happens when you type this:

var ajax = $.get('https://api.doughnuts.ga/doughnuts');
console.log(ajax); -->

### A little more programmatically now

While that's great, it's dangerously asynchronous. How do we build this so that it `console.log`'s the response when an AJAX request actually succeeds, instead of right after it runs in the console?

```js
  var ajax = $.get('https://api.doughnuts.ga/doughnuts')
    .done(function(data){
      console.log(data);
    });
```

Try that out. If there's any luck, our connection will be a little slow, and you'll see the pause between when we hit enter & when it spits out our JSON result.

In jQuery's documentation you can find all the chain-able callback function possibilities – the three you'll probably use a lot are `.done`, `.fail`, and `.always`.

<!--11:50 20 minutes -->

## Independent Practice

Now, using your console, I want to you to try a few more API tasks on this endpoint:

- Get a single donut and spit out the JSON collection in the console
- Use the more generic [$.ajax](http://api.jquery.com/jquery.ajax/) to do the same request
- Modify that donut by changing its flavor
- Add a new donut to the list with style and flavor

<!--12:10 10 minutes -->
## Conclusion (5 mins)
- What's the main use case of AJAX? Why would anyone use it?
- How do you do a simple GET request in vanilla JS?
- How do you do a GET request with jQuery?
- How do you do a PUT, POST, or DELETE request in jQuery?

#### Extra Reading
- [`No 'Access-Control-Allow-Origin' header is present on the requested resource` – WTF?](https://jvaneyck.wordpress.com/2014/01/07/cross-domain-requests-in-javascript/)
- [What is Cross Origin Resource Sharing (CORS)?](https://www.maxcdn.com/one/visual-glossary/cors/)
- [Using CORS with Express](http://enable-cors.org/server_expressjs.html)
