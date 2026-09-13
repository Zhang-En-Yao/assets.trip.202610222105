# assets.trip.202610222105

Madrid, the Camino Francés and Barcelona, October–November 2026 — everything the trip's page on
[zhang-en-yao.github.io](https://zhang-en-yao.github.io) reads, in one place. The site
holds the framework and a routing table; a trip holds itself.

```
https://cdn.jsdelivr.net/gh/Zhang-En-Yao/assets.trip.202610222105@<tag>/content.json
```

The site pins that tag in `travel/index.json`, so nothing published here reaches the live
page until the pin moves. A trip that is finished never needs the pin moved again.

## What is here

| File | What it is | Written by |
| --- | --- | --- |
| `content.json` | the travelogue: sections, points, the prose, the daily itinerary | by hand |
| `places.json` | the Wikidata record behind each point that has a `wikidata` key | `build-places.py` |
| `streets.json` | OpenStreetMap geometry for the cluster maps, keyed by map | `build-streets.py` |
| `MANIFEST.json` | what the last build produced — the drift check compares against it | `validate-trip.py` |

No photographs yet — when there are, they go in `photos/`, referenced by file name from
`content.json`.

`content.json` is the only file edited by hand, and it is the source of every other one.
A point's `wikidata` key is the join: `places.json` is keyed by those QIDs, and the map
keys in `streets.json` are derived from the section and subsection headings.

## Rebuilding

The scripts live in [assets.core](https://github.com/Zhang-En-Yao/assets.core) so every
trip shares one copy. From a clone of this repository, with assets.core beside it:

```
python3 ../assets.core/build/build-places.py     # refresh from Wikidata
python3 ../assets.core/build/build-streets.py    # fetch maps that are missing
python3 ../assets.core/build/validate-trip.py    # check the three files agree, and list the gaps
```

The Actions workflow runs the same three, on request.

## What validation checks

Not that each file is well formed — that they agree with each other:

- every `wikidata` key in `content.json` has a record in `places.json`, and no record is
  there that nothing links to
- every World Heritage site cited by a record is present
- `streets.json`'s keys are exactly the map keys `build-streets.py` derives from the
  headings, so renaming a section cannot silently orphan its maps
- nothing has lost more than a twentieth of its rows since the last build

Nothing here guesses. A point is linked only if `content.json` says so, and `build-places.py`
fetches exactly the QIDs it finds — it never searches for a name, never picks a nearby item,
and never writes a coordinate back. What it cannot know, it reports:

- points with no `wikidata` key yet
- linked points Wikidata holds no coordinate for
- points sitting more than 150 m from Wikidata's coordinate

Those three lists print on every validation run and go to the workflow's summary page. None
of them fail the build — a cafe with no Wikidata item is a permanent, correct state — and
none of them are fixed automatically. They are for reading, and for editing `content.json`
by hand.

## Licences

The prose is En-Yao's. `places.json` is derived from Wikidata (CC0).
`streets.json` is derived from OpenStreetMap, © OpenStreetMap contributors, ODbL — the
site credits it under every map that draws it.
