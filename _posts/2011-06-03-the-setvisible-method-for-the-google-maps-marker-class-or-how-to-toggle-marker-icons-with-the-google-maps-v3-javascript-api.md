---
layout: post
title: "The setVisible method for the google.maps.Marker class – or how to toggle marker icons with the Google Maps v3 JavaScript API."
description: ""
category: 
tags: []
---
{% include JB/setup %}

Toggle marker’s visibility can become a very handy feature especially when displaying a lot of markers at once. The most common way – as i have seen it on over a few hundred maps now – is to just set all marker objects to null and plot the new selection again, what is far away from being the best choice when it comes to performance (and usability). Therefore the Maps Javascript API offers methods, so you can easily alter the visibilty of objects.

### Live Demo

For all the cherry pickers, first of here is a copy-paste ready live demo:
http://demos.bexton.net/gmaps/v3/flexible-toggle.html

### Toggle visibilty by attribute (e.g. category):

Let’s start at how to assign data to the marker objects:

As Javascript is a dynamic language, you can just add your custom attributes (and values) to the (marker) object itself. That means you can:

```js
// create a new google.maps.Marker class object
var marker = new google.maps.Marker(markerOptions);
// and add some custom data to the marker object
marker.customdata = {category: "drinking_beer", bottles: 10};
// and retrieve the data like
var drunken = marker.customdata["bottles"];
```

And because all v3 objects extend MVCObject(), you can also:

```js
// add data with setValues
marker.setValues({category: "drinking_beer", bottles: 10});
// and/or the set method
marker.set("category", "drinking_beer");
marker.set("bottles", 10);
// and retrieve them via get
var drunken = marker.get("bottles");
```

**UPDATE:** Of course you also assign the custom data directly when initializing the marker object (like i did it in the example).

```js
var customdata = {
    category: 'drinking_beer',
    bottles: 2
}
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(50.94158292629882,6.958497762680054),
    map: map,
    customdata: customdata
});
```

### The setVisible method

I’ve seem some interesting implementations like marker.visible = false or setVisible(marker, false) and much more.
But the proper way of using the setVisible method for the google.maps.Marker class is:

```js
// @param  visible   boolean  true/false
marker.setVisible(false);
```

### The getVisible method

To get the current visibilty status, use:

```js
// @return boolean
marker.getVisible();
```

### My toggle function

That being said, it’s really easy to hide and show markers with just a few lines of code:

```js
// toggle the visibility of all markers
function toggleMarkers() {
  if (markers) {
    for (i in markers) {
	var visibility = (markers[i].getVisible() == true) ? false : true;
	markers[i].setVisible(visibility);
    }
  }
}
```

Remembering the custom data assignment, we can now easily apply ours custom filter with just an extra if condition:

```js
// toggle the visibility - filtered
function toggleMarkers(attr,val) {
  if (markers) {
    for (i in markers) {
        if(markers[i].cutomdata[attr] == val){
	    var visibility = (markers[i].getVisible() == true) ? false : true;
	    markers[i].setVisible(visibility);
        }
    }
  }
}
```

Throwing all together, looks like this:

```js
var markers = [];
function initialize() {
    var Latlng1 = new google.maps.LatLng(50.94158292629882,6.958497762680054);
    var Latlng2 = new google.maps.LatLng(50.94372591509999,6.957961320877075);
 
    var mapOptions = {
        zoom: 14,
        center: Latlng1,
        mapTypeId: google.maps.MapTypeId.ROADMAP
    }
    var map = new google.maps.Map(document.getElementById("map_canvas"), mapOptions);
 
    var markerData1 = {
        category: 'drinking_beer',
        bottles: 2
    }
    var marker1 = new google.maps.Marker({
        position: Latlng1,
        map: map,
        data: markerData1
    }); markers.push(marker1);
 
    var markerData2 = {
        category: 'drinking_beer',
        bottles: 10
    }
    var marker2 = new google.maps.Marker({
        position: Latlng2,
        map: map,
        data: markerData2
    }); markers.push(marker2);
}
 
function toggleMarkers(attr,val) {
    if (markers){
        for (i in markers) {
            if(markers[i].data[attr] == val){
                var visibility = (markers[i].getVisible() == true) ? false : true;
                markers[i].setVisible(visibility);
            }
        }
    }
}
```

The “cool” thing about it: it’s really flexible. As we’re not using any hard coded references / identifiers (except the markers), we have a very flexible function. We can apply as much filter as we want / need (by category, by id, etc). For example:

```js
toggleMarkers('category','drinking_beer');
toggleMarkers('bottles',10);
```

Give it try and check out the live demo, using the code above:
http://demos.bexton.net/gmaps/v3/flexible-toggle.html

Happy toggling!  
