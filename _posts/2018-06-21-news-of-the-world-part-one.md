---
published: false
title: News of the World - Part One
layout: post
excerpt: Choosing and configuring the Leaflet library
comments: true
---
News of the World is a (very) small JavaScript project about showing local news headlines on a world map. All the ideas, pitfalls and workarounds found during development will be documented here in a semi-blog/semi-tutorial. This is part one.

## Reinventing the Wheel

One of the more common adages is "don't reinvent the wheel". The idea that part of the software you're trying to build has already been built by someone else and built better, so you should just use that. There are reasons to _not_ invent the wheel, of course, but it's quicker, easier and safer to use pre-existing libraries, frameworks and APIs.

Going down this route, however, has its own problem: Which wheels do you actually use? The JavaScript ecosystem is especially enormous, and it's easy to get paralysed with indecision over what to use. Do you use vanilla JavaScript, or do you use a framework? If you're using a framework, do you use Node, Angular, React, Vue, or something else entirely? If you're using a framework, do you need a package manager, a build system and a transpiler? If you do, which ones do you choose? If you're on the peripheries of web development, like I am, some of these decisions can seem impossible. [_How it feels to learn JavaScript in 2016_](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f) is a slightly hyperbolic and very relatable article of what being in this position is like. In the end, I settled on using pure JavaScript, because many of the frameworks seem to revolve around making single-page apps. While News of the World only uses a single page, this is because it's simple enough to do so anyway.

With a language nailed down, it was time to decide on which libraries and APIs to use. It's useful to think about what it is your application is doing, and dividing up those basic features into services that can perform them.

In this case, News of the World does two simple things:

- Show a map for the user to click on.
- Show news headlines for the region the user clicked.

In terms of services, this can be thought of as:

- Something to show the map.
- Something to determine the location the user clicked on.
- Something to get the headlines from that area.
- Something to show those headlines.

It takes a mixture of prior knowledge and experience and some searching around to figure out which libraries to use. My (rather small amount of) prior knowledge and experience got me as far as suggesting some potentially existing Google News API to get the headlines and a Bootstrap modal popup to show them.

As it turns out, the potentially existing Google News API didn't actually exist. Or, rather, it had been deprecated and replaced with a restructive, paid service in typical Google fashion. I had a short look at [News API](https://newsapi.org/), which admittedly has a very generous free package for non-commercial, open-source developers, but the daily rate limit made me uneasy. Fortunately, [a kind Stack Overflow answer](https://stackoverflow.com/a/7829688/) provided a sneaky workaround for the Google problem: while the official API had been replaced, you could still request an RSS feed for a more stripped-down search result. It's not as ideal as an API, but it's workable.

As for the mapping library itself, the first one I stumbled on was [Leaflet,](https://leafletjs.com/) which turns out to be pretty much perfect! It's very lightweight and easy to get up off the ground. Some people might find it lacking for anything advanced, but for loading and interacting with a map of the world, it's probably the best JavaScript library out there.

## Learning Leaflet

Leaflet is a beautifully simple mapping library that handles all the layers of more advanced, paid software like Google Maps or ESRI, made up of:

- The tile layer: A series of raster or vector images assembled in a grid that forms the base of the map.
- The overlay layer: Any additional markers, lines or polygons that are drawn over the tile layer.
- The popup layer: An additional layer for popups to appear on if you click on the tile layer or an element in the overlay layer.
- The control layer: Controls for panning and zooming across the map.

Given how much it takes for even the basic elements of a mapping library, you can see why it's much easier to not reinvent the wheel. Look through [Leaflet's GitHub page](https://github.com/Leaflet/Leaflet) for a bit to see how much work went into its 378 KB of source code.

I needed Leaflet to be able to show a map and determine the location that map is clicked on, and, as luck would have it, both of these features are detailed in their [quick start guide!](https://leafletjs.com/examples/quick-start/)

Setting up a Leaflet map is as easy as creating a `<div>` element in the body of an HTML page:

```html
<div id="map" style="width: 600px; height: 600px;"></div>
```

Creating a map, assigning it to the `<div>` element, and giving it a pair of latitude/longitude coordinates and a zoom level:

```javascript
let map = L.map('map').setView([54, -2.5], 5);
```

Choosing a provider for the images to go on the tile layer:

```javascript
L.tileLayer('https://api.tiles.mapbox.com/v4/{id}/{z}/{x}/{y}.png?access_token=pk.eyJ1IjoibWFwYm94IiwiYSI6ImNpejY4NXVycTA2emYycXBndHRqcmZ3N3gifQ.rJcFIG214AriISLbB6B5aw', {
  maxZoom: 18,
  attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, ' +
  	'<a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, ' +
  	'Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
  id: 'mapbox.streets'
}).addTo(map);
```

Creating a popup and assigning a function to that popup:

```javascript
let popup = L.popup();

function onMapClick(event) {
  popup
    .setLatLng(event.latlng)
    .setContent(event.latlng.toString())
    .openOn(map);
}
```

Attaching that function to the map:

```javascript
map.on('click', onMapClick);
```

And that's a fully-functional map in less than 20 lines of code!

![leaflet map]({{ site.url }}/assets/img/leaflet-map.png)

## Colouring In

Given Leaflet's simplicity, you might not expect it to be very customisable, but there's a _lot_ of scope in the base library to tweak it how you want. One of the main functionalities is being able to swap in and out tile layers to entirely change how your map looks. This is as easy as changing the URL when creating a `TileLayer`. Like a lot of other open-source projects, Leaflet enjoys a lot of support from the rest of the open-source community who provide free-to-use layers. The [leaflet-providers](https://github.com/leaflet-extras/leaflet-providers) extension streamlines the creation of layers from different providers (although it's easier and more lightweight to just nab the code from their very handy [preview page](https://leaflet-extras.github.io/leaflet-providers/preview/)). Out of all of these, the most eyecatching one was Stamen's Watercolor layer. It doesn't come with any labels, but Stamen also provide a standalone layer for labels. After changing the URL, adding another layer is done in exactly the same way as the first:

```javascript
L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/watercolor/{z}/{x}/{y}{r}.png', {
  maxZoom: 18,
  attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, ' +
  	'<a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, ' +
  	'Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
  id: 'mapbox.streets'
}).addTo(map);

L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/toner-hybrid/{z}/{x}/{y}{r}.png', {
  maxZoom: 18,
  attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/">OpenStreetMap</a> contributors, ' +
  	'<a href="https://creativecommons.org/licenses/by-sa/2.0/">CC-BY-SA</a>, ' +
  	'Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
  id: 'mapbox.streets'
}).addTo(map);
```

And that's all it takes to get a fantastic-looking watercolour map!

![watercolour map]({{ site.url }}/assets/img/watercolour-map.png)
