# Introduction

The ListenBrainz Content Resolver resolves global JSPF playlists to
a local collection of music, using the resolve function.

ListenBrainz Local Radio allows you to generate tag radio playlists that
can be uploaded to your favorite subsonic API enabled music system.

## Quick Start

To install the package:

```
python -m venv .virtualenv
source .virtualenv/bin/activate
pip install -r requirements.txt
```

## Scanning your collection

Then prepare the index and scan a music collection. mp3, m4a, wma, OggVorbis, OggOpus and flac files are supported.

```
./resolve.py create music_index
./resolve.py scan music_index <path to mp3/flac files>
```

If you remove from tracks from your collection, use cleanup to remove refereces to those tracks:

```
./resolve.py cleanup music_index
```

## Resolve JSPF playlists to local collection

Then make a JSPF playlist on LB:

```
https://listenbrainz.org/user/{your username}/playlists/
```

Then download the JSPF file (make sure the playlist is public):

```
curl "https://api.listenbrainz.org/1/playlist/<playlist MBID>" > test.jspf
```

Finally, resolve the playlist to local files:

```
./resolve.py playlist music_index input.jspf output.m3u
```

Then open the m3u playlist with a local tool.

## Create playlists with ListenBrainz Local Radio

### Prerequisites

NOTE: This feature only works if you music collection 
is tagged with MusicBrainz tags. (We recommend Picard:
http://picard.musicbrainz.org ) and if your music
collection is also available via a Subsonic API.

### Setup

In order to use the LB Local Radio playlist generator you'll need
to download more data for your MusicBrainz tagged music collection.

First, download tag and popularity data:

```
./resolve.py metadata music_index
```

Then, copy config.py.sample to config.py and then edit config.py:

```
cp config.py.sample config.py
edit config.py
```

Fill out the values for your subsonic server API and save the file.
Finally, match your collection against the subsonic collection:

```
./resolve.py subsonic music_index
```

### Playlist generation

Currently artist and tag elements are supported for LB Local Radio,
which means that playlists from these two elements are made from the local 
collection and thus will not need to be resolved. All other elements
may generate playlists with tracks that are not availalble in your
collection. In this case, the fuzzy search will attempt to make the
missing tracks to your collection.

For a complete reference to LB Radio, see:
[ListenBrainz Radio Docs](https://troi.readthedocs.io/en/latest/lb_radio.html)

The playlist generator works with a given mode: "easy", "medium"
and "hard". An easy playlist will generate data that more closely
meets the prompt, which should translate into a playlist that should
be easier and pleasent to listen to. Medium goes further and includes
less popular and more far flung stuff, before hard digs at the bottom
of the barrel. 

This may not always feel very pronounced, especially if your collection
isn't very suited for the prompt that was given.


#### Artist Element

```
./resolve.py lb-radio music_index easy 'artist:(taylor swift, drakee)'
```

Generates a playlist with music from Taylor Swift and artists similar
to her and Drake, and artists similar to him.


#### Tag Element

```
./resolve.py lb-radio music_index easy 'tag:(downtempo, trip hop)'
```

This will generate a playlist on easy mode for recordings that are
tagged with "downtempo" AND "trip hop".

```
./resolve.py lb-radio music_index medium 'tag:(downtempo, trip hop)::or'
```

This will generate a playlist on medium mode for recordings that are
tagged with "downtempo" OR "trip hop", since the or option was specified
at the end of the prompt.

You can include more than on tag query in a prompt:

```
./resolve.py lb-radio music_index medium 'tag:(downtempo, trip hop)::or tag:(punk, ska)'
```

#### Stats, Collections, Playlists and Rec

There are more elements, but these are "global" elements that will need to 
have their results resolved to the local collection. The resolution process is
always a bit tricky since its outcome heavily depends on the collection. The
generator will do its best to generate a fitting playlist, but that doesn't
always happen. 

For the other elements, please refer to the 
[ListenBrainz Radio Docs](https://troi.readthedocs.io/en/latest/lb_radio.html)

## Other features

### Collection deduplication

The "duplicates" command will print a report of duplicate recordings
in your collection, based on MusicBrainz Recording MBIDs. There are several
types of duplicates that this may find:

1. Duplicated tracks with the same title, release and artist.
2. Duplicated tracks that live on different releases, but have the same name
3. Duplicated tracks that exist once on an album and again on a compilation.

If you specify -e or --exclude-different-release, then case #3 will not be shown.

### Top tags

The top-tags command will print the top tags and the number of times they
have been used in your collection. This requires that the "metadata"
command was run before.
