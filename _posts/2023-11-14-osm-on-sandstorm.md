---
layout: post
title: "Desert Atlas: A Self-Hosted OpenStreetMap App for Sandstorm"
author: Daniel Krol
authorUrl: https://github.com/orblivion
---

*Hi, my name is [Dan](https://danielkrol.com). This is my first time posting on the Sandstorm blog. I learned about Sandstorm almost a decade ago. I was interested in resiliency and was looking at easy self-hosting solutions. I thought it would be really cool to run Sandstorm "off the grid", even on a mesh network. We have a lot of applications easy to install, but I thought it would be cool to bring easy hosting of Wikipedia and other open data to Sandstorm, so I packaged an application called [Kiwix](https://apps.sandstorm.io/app/5uh349d0kky2zp5whrh2znahn27gwha876xze3864n0fu9e5220h) (which is an awesome project [in its own right](https://kiwix.org)).*

*As I was wrapping up with that, I decided that some day I would do the same for [OpenStreetMap](https://openstreetmap.org). But unlike with Kiwix, I needed to create a new application. Years later, here we are.*

Introducing Desert Atlas
------------------------

Sandstorm, meet OpenStreetMap. OpenStreetMap, meet Sandstorm.

[Desert Atlas](https://apps.sandstorm.io/app/e5eaqnrqfrhgax1awtgw9uqayg42kcen2gkpynjs3j5mww7w3rp0) is the world map for Sandstorm. With Desert Atlas, you can privately collaborate with friends to search for destinations and save them as bookmarks for use on the go.

[<img src="/news/images/tryitnow_purp1.svg" alt="Try It Now!" title="" width="400px" />](https://demo.sandstorm.io/appdemo/e5eaqnrqfrhgax1awtgw9uqayg42kcen2gkpynjs3j5mww7w3rp0)

You may be familiar with OpenStreetMap applications for your phone such as Organic Maps that preserve privacy by downloading entire regions of the map at once and performing all other actions locally. Desert Atlas was inspired by this model. Unlike many OpenStreetMap web applications, the map regions are fully hosted in your grain, and downloaded with the same ease of point-and-click that you expect from Sandstorm. Unlike Organic Maps, Desert Atlas also allows you to easily share your map with a friend and plan a trip together, all on a private server that you trust.

![Screenshots, downloading and searching in El Paso, Texas](/news/images/desert-atlas-el-paso-example.png)

When you're ready to take your map on the road, Desert Atlas its also a companion for Organic Maps and OsmAnd. You can export your bookmarks from your sandstorm grain to your OSM app on your phone and navigate privately.

![Screenshot, using "Export To App" feature to export bookmarks to Organic Maps"](/news/images/desert-atlas-export-to-app.png)

This has been the result of quite a bit of effort on my part, including learning more about the OpenStreetMap ecosystem I'd never thought I would. But since I had to learn a lot of things, I didn't learn them very deeply. If you're inspired by this project and have deeper knowledge about some of its components, I've listed some low hanging fruit at the end of this post where you might be able to make a big impact pretty easily.

The gap between Organic Maps and Google Maps
--------------------------------------------

Organic Maps allows you to search for destinations, navigate, and create bookmarks that can be imported or exported as [kml files](https://en.wikipedia.org/wiki/Keyhole_Markup_Language). It does all of these on your phone without hitting the network. The one concession to privacy is that the map data has to come from somewhere. The underlying map regions need to be downloaded initially, and updated periodically. Organic Maps' server knows what regions you're downloading, but they're each roughly the size of a small country, and it happens maybe once a week. This gives them much lower resolution than, say, requesting a specific intersection on demand from a centralized map website.

![Diagram of Organic Maps: One arrow from Map Data server for Organic Maps to Organic Maps phone app indicates downloading of map regions. Arrows in either direction indicate import/export of bookmarks from Organic Maps phone app and unspecified means of sharing with friends."](/news/images/desert-atlas-diagram-organic-maps.png)

But what if you want to share the location of an event with a friend? Exporting and sending bookmark files is a bit inconvenient, especially if you're collaborating. Organic Maps actually has a feature for easily sharing locations with a friend, but it uses their website, which means you'll be revealing the specific location to Organic Maps' server. For collaboration, you might find yourself using a different centalized service (Google Maps comes to mind but there are OSM-based options), even if you export the results to your Organic Maps app.

This is where a private web app comes in handy. It sits on the Internet, which is less secure than Organic Maps, so you may not want to use it for everything. But if you trust the server, it can still be private. You can share it with chosen friends, and plan together if you want. When you're done, you can each export it Organic Maps for navigation, and better convenience and security.

![Screenshot, list of Desert Atlas grains: "Favorite Seacoast Cafes", "Montréal trip", "Campsites for May 7, 2024", and "Parking Near My Apartment"](/news/images/desert-atlas-grain-listing.png)

For comparison, I've looked at a couple other options for self-hosted OSM. I haven't taken the time to try them out in depth, so apologies if I get something wrong.

[Facilmap](https://yunohost.org/en/app_facilmap) for YunoHost and [Nextcloud Maps](https://github.com/nextcloud/maps) are made to be easy to set up and have similar use cases to Desert Atlas. They let you bookmark, plan trips, and share the results, all privately. But rather than downloading regions like Organic Maps, [they get underlying map data from and sends search terms to](https://github.com/nextcloud/maps/issues/733) third-party services (such as openstreetmap.org), which [leaks some](https://docs.facilmap.org/users/privacy/#layers) usage information.

[Headway](https://about.maps.earth/) is a project that solves this privacy problem. It installs the "full stack" of self-hosted OSM relatively easily. As you'll see below, the OSM ecosystem has a lot going on, and Headway simplifies things quite a bit. However, it uses Docker, which (in the humble opinion of Sandstorm fans) is still not as simple as Sandstorm. (I have seen a little discussion about porting it to YunoHost, but it hasn't happened as of now).

Desert Atlas takes a much more stripped down approach than Headway. Built for Sandstorm, with a lot fewer moving parts. I'm not aware of any other offering that is quite as easy to spin up (thanks to Sandstorm), lets you point and click to download the regions you need, allows you to collaborate with friends, and gives you this level of privacy.

![Diagram of Sandstorm + Desert Atlas: One arrow for bookmarks export from phone web browser to Organic Maps. One arrow from Map Data Server for Desert Atlas to Sandstorm Server indicates downloading of map regions. Arrows in both directions indicating all user activity being sent between web browsers and Sandstorm Server."](/news/images/desert-atlas-diagram-sandstorm.png)

The Simplest Version of Everything
----------------------------------

So how does this actually work? I keep it simple, which is not to say easy! The implementation wasn't that hard. The hard part was finding which tools to use and how to intergrate them. All the heavy lifting was already implemented by those tools. Big thanks to all who created them.

OpenStreetMap is, among other things, a canonical database representing the world map. It's openly available and editable like Wikipedia. OSM applications such as Desert Atlas need to download this data in one way or another. Application creators usually provide their own copy of the data in a custom format that their apps download. They also use their own servers to spare OSM's own resources. OpenStreetMap provides periodic snapshots of their data in one giant protobuf file that application creators can download to make periodic snapshots of the data available to their users.

In general, the two parts of the OSM app experience are tiles and search.

### Tile format

The traditional OpenStreetMap stack includes a tile server, which imports the raw protobuf data into a postrgres database and generates png squares at every zoom level. Postgres is hard to get running on Sandstorm because it's made to be multi-user (though at one point I actually got a tile server to run and generate tiles in a test environment!). Alternately, as the provider of the map data for the app, I could pre-generate the png files for the app to download, but I suspect the resulting file size would be massive.

But then one day on Hacker News I saw something called Protomaps(link). It's a project for vector-based tiles that render in the browser. You can have a data file in a format called pmtiles to represent a region. Then you can use protomaps.js(link) (which works with the Leaflet UI framework) that makes range requests for the specific data it needs for whatever zoom level. No need for a database, or to generate anything on the app. When I prepare the map data for download, instead of generating countless png files, I generate one pmtiles file per region.

And how do we get our pmtiles files? I didn't want to regularly copy the entire world map from protomaps.com. I set out to generate them myself from raw OSM data. The best way I could find was to first convert them to another vector format called mbtiles using a tool called tilemaker(link), and then from mbtiles to pmtiles using go-pmtiles(link) from the Protomaps project.

### Tile Schema

And... it didn't work. I generated my first pmtiles file and nothing showed up. It turns out that on top of tile file formats, there's a concept of a schema. Protomaps.js turns out to have its own schema. The schema is determined in tilemaker using a lua script, and the default lua script it come with generates according to the OpenMapTiles schema. I initially set out to edit this lua file until the data looked like the Protomaps schema. But I was a little nervous because OpenMapTiles apparently has an uncertain intellectual property situation(link to github discussion on this). Fortunately the folks at Geofabrik created their own schema called Shortbread, licensed CC0, and they made an accompanying lua script for tilemaker. To steer clear of any of those IP issues, in creating my Protomaps based schema file, I started from the Shortbread lua file.

### Search Data

The other part of the OSM experience is search. The standard option is Nominatim. It uses ElasticSearch under the hood, which again is a bit heavy duty for Sandstorm. I asked myself, "what is the sqlite of search?" It turns out the answer is... sqlite! There's a plugin called fts5 that performs reasonably well on Desert Atlas for searching names in the database (whether it can be used to handle addresses remains to be seen). To generate the search database, I decide what I want to extract(link to my script) from the raw protobuf using pyosmium(link) and save to a csv. When the app downloads a region, the search csv is imported into the grain's sqlite search database. Addresses, I'll still have to figure something out.

I decided that it would be useful to have cities and large towns built into the app so that the user can jump to their city by searching, and then download the region from there. For this, I used a pre-baked non-OSM database called GeoNames(link) as a shortcut.

### Splitting the world

Organic Maps is generally split by "administrative region". It could be a country, state, or metro area, depending on how dense the information is. Ideally I would have done this.

There's a service called Geofabrik that offers such regions for download, but they have a rate limit that's prohibitive for this purpose. They also have a publicly available geojson file that defines their regions. However the exact process they use to slice the planet with this definition is not publicly available. Given enough time I could figure it out.

However, I found a shortcut and I decided to take it. I download OSM's own planet.osm.pbf and split it using a tool called Tile Splitter(link https://www.mkgmap.org.uk/doc/splitter.html) from mkgmap (In fact I split twice, because of memory issues). This tool splits into rectangles targeting a maximum raw data size per region. Unfortunately the render and search extractions done for Desert Atlas do not correspond 1:1 with the raw data, so the downloadable regions in Desert Atlas vary quite a bit in size.

This is also a shortcut because it made the UI for downloading regions a lot simpler.

### UI

This one was straightforward. Leaflet is the go-to web UI framework for OpenStreetMap. It's very nice. There's a plugin for search called Leaflet-Search that worked for my needs.

### So, in a nutshell...

Periodic map generation process (currently ~2.5 days)
* Download planet.osm.pbf (world map raw protobuf)
* plant.osm.pbf -> rectangular region osm.pbf files (using mkgmap)
* each region osm.pbf -> region .pmtiles with protomaps schema (using tilemaker and go-protomaps, with thanks to shortbread schema)
* each region osm.pbf -> region .csv with search data (pyosmium)
* each region .pmtiles + .csv -> region .tar.gz -> upload to S3

On Sandstorm, each grain is pre-loaded with large towns thanks to data taken from GeoNames. It downloads whichever regions the user asks for. For each region, the pmtiles file is ready to use as-is by protomaps.js and Leaflet, and the search csv file is imported into the sqlite+fts5 search database, with Leaflet-Search providing the UI.

Getting involved
----------------

If you're an OSM developer, you may be facepalming right now. But you also know that OSM has a big, confusing ecosystem, with many ways of doing the same thing. This was my first exposure to a lot of it. This was a lot of work, and I wanted to forge a reasonable but efficient path to a minimum viable product.

It's a modest submission of a usable proof of concept. I didn't spend much time getting hung up on finding the very best way of doing everything on the first try. But at this point I'm wide open to replacing any part of this with something better.

I've [identified some areas](https://github.com/orblivion/desert-atlas/wiki/Where-to-help) that I think could be low hanging fruit, especially for people who can teach me about areas I'm less famailar with (OSM, UI, front end, etc). Hit me up if you'd like to help!
