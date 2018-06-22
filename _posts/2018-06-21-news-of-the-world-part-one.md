---
published: true
title: News of the World - Part One
layout: post
excerpt: Choosing and configuring the Leaflet library
comments: true
---
News of the World is a (very) small JavaScript project about showing local news headlines on a world map. All the ideas, pitfalls and workarounds found during development will be documented here in a semi-blog/semi-tutorial. This is part one.

## Reinventing the wheel

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
  attribution: 'Map data &copy; ' + 
    '<a href="https://www.openstreetmap.org/">OpenStreetMap</a> ' +
    'contributors, ' +
    '<a href="https://creativecommons.org/licenses/by-sa/2.0/">' +
    'CC-BY-SA</a>, ' +
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

## Colouring in

Given Leaflet's simplicity, you might not expect it to be very customisable, but there's a _lot_ of scope in the base library to tweak it how you want. One of the main functionalities is being able to swap in and out tile layers to entirely change how your map looks. This is as easy as changing the URL when creating a `TileLayer`.

Like a lot of other open-source projects, Leaflet enjoys a lot of support from the rest of the open-source community who provide free-to-use layers. The [leaflet-providers](https://github.com/leaflet-extras/leaflet-providers) extension streamlines the creation of layers from different providers (although it's easier and more lightweight to just nab the code from their very handy [preview page](https://leaflet-extras.github.io/leaflet-providers/preview/)). Out of all of these, the most eyecatching one was Stamen's Watercolor layer. It doesn't come with any labels, but Stamen also provide a standalone layer for labels. After changing the URL, adding another layer is done in exactly the same way as the first:

```javascript
L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/watercolor/{z}/{x}/{y}{r}.png', {
  maxZoom: 18,
  attribution: 'Map tiles by <a href="http://stamen.com">' +
    'Stamen Design</a>, ' +
    '<a href="http://creativecommons.org/licenses/by/3.0">' +
    'CC BY 3.0</a> &mdash; ' +
    'Map data &copy; ' +
    '<a href="http://www.openstreetmap.org/copyright">' +
    'OpenStreetMap</a>',
  id: 'mapbox.streets'
}).addTo(map);

L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/toner-hybrid/{z}/{x}/{y}{r}.png', {
  maxZoom: 18,
  attribution: 'Map tiles by <a href="http://stamen.com">' +
    'Stamen Design</a>, ' +
    '<a href="http://creativecommons.org/licenses/by/3.0">' +
    'CC BY 3.0</a> &mdash; ' +
    'Map data &copy; ' +
    '<a href="http://www.openstreetmap.org/copyright">' +
    'OpenStreetMap</a>',
  id: 'mapbox.streets'
}).addTo(map);
```

And that's all it takes to get a fantastic-looking watercolour map!

![watercolour map]({{ site.url }}/assets/img/watercolour-map.png)

## Off the edge of the world

Remember when I said the Leaflet map was fully-functional? That was a little bit of a lie. While it works as a map, there's a few edge cases that need some more options to sort out. And I mean literal 'edge' cases, because they're to do with the boundaries of the map.

There are a few problems that our Leaflet configuration has right now, namely:

- You can pan indefinitely up and down into a grey void of nothingness.
- You can zoom so far out that you can see three copies of the world side-by-side.
- Panning either left or right wraps the map nicely, but the latitude doesn't wrap with it.

Discovering bugs is usually done through a series of rigorous and comprehensive tests, but in the case of the last problem, it was...not like that. First, a very quick refresher on how coordinates work. Any point on the earth can be mapped to a pair of points: the latitude (along a horizontal line between -180° and 180°) and the longitude (along a vertical line between -90° and 90°). Because the Earth is, well, round (sorry, flat-earthers), if you go past either end of the latitude or longitude lines, it wraps around right to the other end.

While I was admiring the watercolour map, I saw an unlabelled island to the north-west of Fiji. I wondered what they were, and rather than looking it up normally, I thought I'd get the coordinates and look those up! Fiji has a latitude of 179°, so I was expecting the coordinates for this island to be about -170°, but it was...190°. Oh.

As it turns out, Leaflet's tile layer wraps around by default, but the latitudes and longitudes from the click events don't. Luckily, this is a very easy fix! We just need to explicitly tell or coordinate values to wrap.

```javascript
let latlng = event.latlng.wrap();
```

Stopping the map from zooming out too far was another simple fix. The `TileLayer` object has a `minZoom` attribute that caps how far you can zoom out. There's also a `minZoom` atttribute, which I thought I'd set to a sensible level too.

```javascript
let mapMaxZoom = 12;
let mapMinZoom = 3;

let stamenWatercolor = L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/watercolor/{z}/{x}/{y}{r}.png', {
  attribution: 'Map tiles by <a href="http://stamen.com">' +
    'Stamen Design</a>, ' +
    '<a href="http://creativecommons.org/licenses/by/3.0">' +
    'CC BY 3.0</a> &mdash; ' +
    'Map data &copy; ' +
    '<a href="http://www.openstreetmap.org/copyright">' +
    'OpenStreetMap</a>',
  maxZoom: mapMaxZoom,
  minZoom: mapMinZoom
});
```

Stopping the user from panning outside the bounds of the map was a little more challenging, because while the `TileLayer` does have a bounds attribute, you need to set the value of those bounds yourself. This is one of those (embarrassingly many) cases [where you find a magic number solution from Stack Overflow,](https://stackoverflow.com/questions/35563627/how-to-prevent-leaflet-from-loading-tiles-outside-map-area) you stick it in to your code and it just...works, due to the fortuitousness of you having the same setup.

```javascript
let mapBounds = [[-8576 / 2, -8576 / 2], [8576 / 2, 8576 / 2]];

let stamenWatercolor = L.tileLayer('https://stamen-tiles-{s}.a.ssl.fastly.net/watercolor/{z}/{x}/{y}{r}.png', {
  attribution: 'Map tiles by <a href="http://stamen.com">' +
    'Stamen Design</a>, ' +
    '<a href="http://creativecommons.org/licenses/by/3.0">' +
    'CC BY 3.0</a> &mdash; ' +
    'Map data &copy; ' +
    '<a href="http://www.openstreetmap.org/copyright">' +
    'OpenStreetMap</a>',
  maxZoom: mapMaxZoom,
  minZoom: mapMinZoom,
  bounds: mapBounds
});
```

There's also an option to use `LatLngBounds`, but...well...this works! And the map's not going to change, so it's an ugly yet affordable evil.

(Oh, and the island was American Samoa. [You can see it on a map.](https://www.cia.gov/library/publications/resources/the-world-factbook/graphics/ref_maps/political/pdf/world.pdf) It's right on the edge.)


## Addressing the problem

Clicking on the map returns the coordinates, but there isn't an API that searches for news by coordinates (maybe that's a niche I could fill with a lot more knowledge and experience), so the latitude and longitude have to be resolved to an address. [OpenStreetMap's Nomanatin API](https://wiki.openstreetmap.org/wiki/Nominatim) is invaluable for this. It has a method called Reverse Geocoding which does all the resolving for us.

So, let's update the `onMapClick` function to print out the address! Rather than using an `XMLHttpRequest`, I thought I'd use the (relatively) fancy, (relatively) new [Fetch API:](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

```javascript
function onMapClick(event) {
    let latlng = event.latlng.wrap();
    let url = 'https://nominatim.openstreetmap.org/reverse?' +
        'format=json' +
        `&lat=${latlng.lat}` +
        `&lon=${latlng.lng}` +
        '&addressdetails=1';
    fetch(url, {
        cache: 'default',
        credentials: 'same-origin',
        headers: {
            'user-agent': window.navigator.userAgent,
            'content-type': 'application/json'
        },
        method: 'GET',
        referrer: 'client'
    })
    .then(response => response.json())
    .then((output) => {
        console.log(output.address);
    })
    .catch(err => {
        throw err
    });
}
```

This creates an HTTP GET request to OSM's Nominatin API with the latitude and longitude as parameters, JSON-ifies the response, and prints out the values under the `address` key. Let's click on somewhere in central London and see what the output looks like!

```json
{
  "address": {
	"hospital": "St Bartholomew's Hospital",
    "road": "Little Britain",
    "suburb": "Temple",
    "city": "City of London",
    "state_district": "Greater London",
    "state": "England",
    "postcode": "EC1M 6DS",
    "country": "United Kingdom",
    "country_code": "gb"
	}
}
```

Neat! So, we now have a map that gets the address of anywhere you click. For part two, we're going to see how we can use this to search!


