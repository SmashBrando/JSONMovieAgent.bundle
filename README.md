JSON Metadata Agent for Plex
============================

A metadata agent for Plex that reads from JSON files co-located with your media.


Why?
----

Not all metadata is easily accessed online via an HTML web page. There are some websites for which it is impossible to write a traditional Plex metadata agent — for example, those that:

* require username and password authentication, possibly even captcha
* hide information behind javascript
* detect the automated nature of the metadata agent, throttling or blocking scrapers
* perform poorly, crash and timeout
* frequently change their HTML structure

This agent doesn't collect any metadata itself — it simply loads metadata from JSON files found with your media into Plex.

It's designed to work alongside other tools and methods of collecting metadata, be it a custom scraper (it doesn't even have to be written in Python), browser plugin, command line tool or GUI. You could even edit the files by hand :)


Media Preparation
-----------------

At the time of writing, JSON metadata is supported *for movies only*.

To define metadata for a movie, a JSON file named exactly `Info.json` must be present in the same directory as your movie file(s). For example:

```
Movies
  |- Spaceballs (1987)
      |- Spaceballs.mp4
      |- Info.json
      |- Cover.jpg
      |- Background.jpg
```

This means you are limited to a single movie and `Info.json` file per directory.


Example JSON
------------

The structure of the `Info.json` file follows as closely as possible that of the `Movie` model defined by Plex itself (although it's basically undocumented). It should look something like this:

```json
{
    "title": "Spaceballs",
    "original_title": "Spaceballs",
    "summary": "Planet Spaceballs' President Skroob sends Lord Dark Helmet to steal planet Druidia's abundant supply of air to replenish their own, and only Lone Starr can stop them.",
    "year": 1987,
    "originally_available_at": "1987-06-24",
    "rating": 6.6,
    "content_rating": "PG",
    "studio": "STUDIO NAME",
    "duration": 96,
    "roles": [
      {
        "name" : "Mel Brooks",
        "role": "President Skroob/Yoghurt",
        "photo": "https://image.tmdb.org/t/p/w264_and_h264_bestv2/ndFo3LOYNCUghQTK833N1Wtuynr.jpg"
      },
      {
        "name" : "Rick Moranis",
        "role": "Dark Helmet",
        "photo": "https://image.tmdb.org/t/p/w264_and_h264_bestv2/27dRb7OyRGzQP8D4tzyY6dEdmQY.jpg"
      },
      {
        "name" : "Bill Pullman",
        "role": "Lone Starr",
        "photo": "https://image.tmdb.org/t/p/w264_and_h264_bestv2/pIpTEQVbDif8m8OdjAxQKNCj0D6.jpg"
      }
    ],
    "genres": [
      "Adventure",
      "Comedy",
      "Sci-Fi"
    ],
    "collections": [
      "android",
      "lasergun",
      "swordplay",
      "temple",
      "space marine",
      "space battle",
      "space travel",
      "space mission",
      "galaxy",
      "comb",
      "altar",
      "magnet beam",
      "jam",
      "speed of light",
      "plastic surgery",
      "password",
      "shield",
      "nose",
      "spoof"
    ]
}
```

Example Artoo Scraper
------------

If you're like me you hate manually building the info.json file manually. That's where [Artoo](http://medialab.github.io/artoo/scrape/) comes in. By using the follwing structure, it's pretty easy to create a custom scraper to build your Info.json file automatically

```jquery
artoo.scrape('SELECTOR', {
  title: {sel: 'SELECTOR'},
  original_title: {sel: 'SELECTOR'},
  summary: {sel: 'SELECTOR'},
  year: {sel: 'SELECTOR'},
  originally_available_at: {sel: 'SELECTOR'},
  rating: {sel: 'SELECTOR'},
  content_rating: {sel: 'SELECTOR'},
  studio: {sel: 'SELECTOR'},
  duration: {sel: 'SELECTOR'},
  roles: {
    scrape: {
      iterator: 'SELECTOR',
      data: {
        name: {sel: 'SELECTOR'},
        role: {sel: 'SELECTOR'},
        photo: {sel: 'SELECTOR' attr: 'href'}
      }
    }
  },
  genres: {
    scrape: {
    iterator: 'SELECTOR',
    data: 'text'
    }
  },
  collections: {
    scrape: {
    iterator: 'SELECTOR',
    data: 'text'
    }
  }
}, artoo.savePrettyJson(data, [Info.json]));
```
