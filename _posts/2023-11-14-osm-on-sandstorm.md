---
layout: post
title: "Desert Atlas: A Self-Hosted OpenStreetMap App for Sandstorm"
author: Daniel Krol
authorUrl: https://github.com/orblivion
---

*Hi, my name is [Dan](https://danielkrol.com). This is my first time posting on the Sandstorm blog. I got involved with Sandstorm almost a decade ago. I imagined a day when open data would be easily deployed via Sandstorm onto a mesh network (a lofty goal, I know), so I [created a package](https://apps.sandstorm.io/app/5uh349d0kky2zp5whrh2znahn27gwha876xze3864n0fu9e5220h) for an existing application called [Kiwix](https://kiwix.org) for easy hosting of sites like Wikipedia.*

*I still have this vision in mind. Today, I'm announcing the result of a more ambitious effort.*

Introducing Desert Atlas
------------------------

Sandstorm, meet [OpenStreetMap](https://openstreetmap.org). OpenStreetMap, meet Sandstorm.

[Desert Atlas](https://apps.sandstorm.io/app/e5eaqnrqfrhgax1awtgw9uqayg42kcen2gkpynjs3j5mww7w3rp0) is the world map for Sandstorm. With Desert Atlas, you can privately collaborate with friends to search for destinations and save them as bookmarks for use on the go.

[<img src="/logos/app-demo-buttons/tryitnow-purp1.svg" alt="Try It Now!" title="" width="400px" />](https://demo.sandstorm.io/appdemo/e5eaqnrqfrhgax1awtgw9uqayg42kcen2gkpynjs3j5mww7w3rp0)

You may be familiar with OSM phone applications like [Organic Maps](https://organicmaps.app/) that download entire regions of the map at once so you can search and browse privately. Desert Atlas was inspired by this model. Unlike many OSM web applications, the map regions are fully hosted in your Sandstorm grain, and downloaded with the same ease of point-and-click that you expect from Sandstorm. Unlike Organic Maps, Desert Atlas makes it easy to share your map with a friend or plan a trip together on a private server that you trust.

![Screenshots, downloading and searching in El Paso, Texas](/news/images/desert-atlas-el-paso-example.png)

When you're ready to take your map on the road, Desert Atlas is also a companion for Organic Maps (and [OsmAnd](https://osmand.net/)). You can export your bookmarks from your sandstorm grain to your OSM phone app to navigate privately.

![Screenshot, using "Export To App" feature to export bookmarks to Organic Maps"](/news/images/desert-atlas-export-to-app.png)

This has been the result of quite a bit of effort on my part, including learning more about the OpenStreetMap ecosystem than I ever thought I would. But since I had to learn a lot of things, I did not learn them very deeply. If you are inspired by this project and have deeper knowledge about some of its components, I have [layed out some areas](https://github.com/orblivion/desert-atlas/wiki/Where-to-help) where you might be able to make a big impact pretty easily.

The gap between Organic Maps and Google Maps
--------------------------------------------

Organic Maps allows you to search for destinations, navigate, and create bookmarks that can be imported or exported as [KML files](https://en.wikipedia.org/wiki/Keyhole_Markup_Language). It does all of these on your phone without hitting the network. The one concession to privacy is that the map data has to come from somewhere. The underlying map regions need to be downloaded initially and updated periodically. Organic Maps' server knows what regions you are downloading, but they are each roughly the size of a small country, and it happens maybe once a week. This gives them much lower resolution than, say, requesting a specific intersection on demand from a centralized map website.

![Diagram of Organic Maps: One arrow from Map Data server for Organic Maps to Organic Maps phone app indicates downloading of map regions. Arrows in either direction indicate import/export of bookmarks from Organic Maps phone app and unspecified means of sharing with friends."](/news/images/desert-atlas-diagram-organic-maps.png)

But what if you want to bookmark some spots for a night on the town and share them with some friends? Exporting and sending bookmark files is a bit inconvenient. There's a web-based sharing feature for Organic Maps, but it reveals the location to Organic Maps' server. If you want to plan the evening together, sharing KML files is especially inconvenient. You might find yourself using a different centralized service (Google Maps comes to mind but there are OSM-based options), even if you import the results to Organic Maps.

This is where a private web app comes in handy. Instead of collaborating and sharing on a centralized service, you can use Desert Atlas on a trusted Sandstorm server. When you're done, you can each export the locations to Organic Maps for navigation and convenience. (And if you're not sharing or collaborating, you can always stick to Organic Maps. It's probably still more secure than a private web server sitting on the Internet.)

![Screenshot, list of Desert Atlas grains: "Favorite Seacoast Cafes", "Montréal trip", "Campsites for May 7, 2024", and "Parking Near My Apartment"](/news/images/desert-atlas-grain-listing.png)

### Some Similar Offerings

For comparison, I have looked at a couple other options for self-hosted OSM. I have not taken the time to try them out in depth, so apologies if I get something wrong.

[Facilmap](https://yunohost.org/en/app_facilmap) for YunoHost and [Nextcloud Maps](https://github.com/nextcloud/maps) are made to be easy to set up and have similar use cases to Desert Atlas. They let you bookmark, plan trips, and share the results, all privately. But rather than downloading regions like Organic Maps, [they get underlying map data from and send search terms to](https://github.com/nextcloud/maps/issues/733) third-party services (such as openstreetmap.org), which [leaks some](https://docs.facilmap.org/users/privacy/#layers) usage information.

[Headway](https://about.maps.earth/) is a project that solves this privacy problem. It installs the "full stack" of self-hosted OSM relatively easily. As you will see below, the OSM ecosystem has a lot going on, and Headway simplifies setup quite a bit. However, it uses Docker, which (in the humble opinion of Sandstorm fans) is still not as simple as Sandstorm. (I have seen a little discussion about porting it to YunoHost, but it has not happened as of this writing).

Desert Atlas takes a much more stripped down approach than Headway. Built for Sandstorm, with a lot fewer moving parts. I am not aware of any other offering that simultaneously lets you spin it up so easily (thanks to Sandstorm), point and click to download the regions you need, collaborate with friends, and have this level of privacy.

![Diagram of Sandstorm + Desert Atlas: One arrow for bookmarks export from phone web browser to Organic Maps. One arrow from Map Data Server for Desert Atlas to Sandstorm Server indicates downloading of map regions. Arrows in both directions indicating all user activity being sent between web browsers and Sandstorm Server."](/news/images/desert-atlas-diagram-sandstorm.png)

The Simplest Version of Everything
----------------------------------

So how does this actually work? I kept it simple, which is not to say easy! While the implementation was not that hard, it took a lot of effort to find the right tools and learn how to integrate them. All the heavy lifting was already implemented by those tools. Big thanks to all those who created them.

OpenStreetMap is, among other things, a canonical database representing the world map. It is openly available and editable like Wikipedia. OSM applications such as Desert Atlas need to download this data in one way or another. Application creators usually provide their own copy of the data in a custom format that their apps download. They also use their own servers to spare the resources of openstreetmap.org. OpenStreetMap provides periodic snapshots of its database as one giant protobuf file known as `planet.osm.pbf`. Application creators can download it and extract what they need to make periodic snapshots of the data available to their users.

In general, the two parts of the OSM app experience are tiles and search.

### Tile format

The traditional OpenStreetMap stack includes a tile server which imports the raw protobuf data into a Postgres database and generates a grid of square png files for every zoom level. Postgres is hard to get running on Sandstorm because it is made to be multi-user (though at one point I actually got a tile server to run and generate tiles in a test environment!) Alternately, as the provider of the map data for the app, I could have taken the path of pre-generating the png files for the app to download, but I suspect the resulting file size would be massive.

Then one day on Hacker News I saw something called [Protomaps](https://protomaps.com). It's a project for vector-based tiles that render in the browser. It uses a file format called pmtiles. A single pmtiles file represents a region of the map at all zoom levels. You can use [protomaps.js](https://github.com/protomaps/protomaps-leaflet) along with the [Leaflet UI framework](http://github.com/Leaflet/Leaflet) to view it. As you scroll or zoom, it makes range requests for the specific subset of the file that it needs to display it. No need for a database, or to generate anything within the Sandstorm app. Each downloadable region of the map has one pmtiles file.

And how do we create these pmtiles files? I did not want to regularly copy the entire world map from the Protomaps project. I set out to generate them myself from raw OSM data. The best way I could find was to first convert them to another vector format called mbtiles using a tool called [tilemaker](https://github.com/systemed/tilemaker/), and then from mbtiles to pmtiles using [go-pmtiles](https://github.com/protomaps/go-pmtiles) from the Protomaps project.

### Tile Schema

I generated my first pmtiles file and... nothing showed up. It turns out that on top of tile file formats, there is a concept of a *schema*. Protomaps.js turns out to have its own schema. Tilemaker uses a lua script to determine the schema of the mbtiles file it creates. The default lua script that comes with tilemaker is based on the OpenMapTiles schema. I initially set out to start with this lua script and edit it until the data it produced looked like the Protomaps schema. However, I was a little nervous because I was advised that OpenMapTiles has an uncertain intellectual property situation. I may or may not have misunderstood or overreacted, but at any rate I had an alternative. The folks at [Geofabrik](https://www.geofabrik.de/) created their own schema called [Shortbread](https://github.com/shortbread-tiles/shortbread-docs) that is licensed CC0 and comes with its own lua script for tilemaker. To steer clear of any concern over IP issues in creating my lua script to generate files in the Protomaps.js schema, I started from Shortbread.

### Search Data

The standard option for an OpenStreetMap search service is called [Nominatim](https://nominatim.org/). It uses Elasticsearch under the hood, which again is a bit heavy duty for Sandstorm. I asked myself, "what is the SQLite of search?" It turns out the answer is... SQLite! There is a [plugin called FTS5](https://sqlite.org/fts5.html) that performs reasonably well on Desert Atlas for searching names in the database. Desert Atlas does not yet support address search, so it remains to be seen how well suited FTS5 is for that.

To generate the search database, I [decide what I want to extract](https://github.com/orblivion/desert-atlas/blob/658b5f3fb4fbd5dc86d2c4cf27b3a7d1200cba6f/generate-data/extract_search.py) from the raw protobuf for a given region using [pyosmium](https://osmcode.org/pyosmium/). The result is saved to a CSV that gets bundled with the pmtiles file for the same region. When a user downloads a region inside Desert Atlas, the CSV is imported into the grain's SQLite search database.

In addition to searching within downloaded regions, I decided that it would be useful to have cities and large towns built into the app so that the user can search for their city *before* they download any regions. This makes it easier for users to orient themselves and find which region they want to download. For this part, I used a pre-baked non-OSM database called [GeoNames](http://download.geonames.org/export/dump/) as a shortcut.

### Splitting the world

Organic Maps is generally split by "administrative region". It could be a country, state, or metro area, depending on how dense the information is. Ideally Desert Atlas would work the same way.

Geofabrik offers up-to-date raw OSM protobuf data split by regions in such a way. However, they have a download rate limit that I decided was prohibitive for this purpose, so I sought out to download OSM's `planet.osm.pbf` and split it myself. Geofabrik also has a publicly available geojson file that defines the regions they offer for download, which I could in principle use to do the same splitting myself. However, the exact process that they use to slice the planet with this json file is not publicly available. In the long run I would like to figure this out.

For now, I found a "good enough" shortcut and I decided to take it. I split `planet.osm.pbf` using a tool called [splitter](https://www.mkgmap.org.uk/doc/splitter.html) from mkgmap. This tool splits raw OSM data into rectangles targeting a maximum raw data size per region. Unfortunately the render and search extractions done for Desert Atlas do not correspond 1:1 with the raw data, so the downloadable regions in Desert Atlas vary quite a bit in size, but I am shooting for about the same region size as Organic Maps.

### UI

This part was straightforward. As mentioned above, I used Leaflet, which is the go-to web UI framework for OpenStreetMap. It's extensible and worked well for me. I also used a plugin called [Leaflet-Search](https://github.com/stefanocudini/leaflet-search) that I was able to connect to the simple Python based backend and query the SQLite database. 

### So, in a nutshell...

Periodic map generation process (currently ~2.5 days)
* Download `planet.osm.pbf` (world map raw protobuf)
* `planet.osm.pbf` -> rectangular region osm.pbf files (using splitter from mkgmap)
* each region osm.pbf -> region .pmtiles with protomaps schema (using tilemaker and go-protomaps, with thanks to Geofabrik for the shortbread schema which I edited until it became the protomaps schema)
* each region osm.pbf -> region .csv with search data (using pyosmium)
* each region .pmtiles + .csv -> region .tar.gz -> upload to S3

App packaging
* bundle geojson files for low-res world and USA maps
* bundle cities and large towns from GeoNames

When a grain is created on the user's Sandstorm server, the bundled GeoNames data is imported into the grain's SQLite+FTS5 search database. The bundled geojson world and USA maps display thanks to Leaflet. The grain downloads whichever regions the user asks for from S3. For each region, the pmtiles file is ready to use as-is by protomaps.js and Leaflet, and the search CSV file is imported into the search database, with Leaflet-Search providing the UI.

Getting involved
----------------

If you are an OSM developer, you may or may not be facepalming right now. Likely some part of this could have been done in a better way. But you also know that OSM has a big, confusing ecosystem, with many ways of doing the same thing. This was my first exposure to a lot of it. This was a lot of work, and I wanted to forge a reasonable but efficient path to a minimum viable product.

It's a modest submission of a usable proof of concept. I did not spend much time getting hung up on finding the very best way of doing everything on the first try, but at this point I am wide open to improvements, including replacing any part of this with something better.

[See here](https://github.com/orblivion/desert-atlas/wiki/Where-to-help) for some areas that I think could be low hanging fruit, especially for people who can teach me about areas I am less familiar with (tiles, search, UI, front end) or just general improvements (rewriting the server in Go). Hit me up if you would like to help!
