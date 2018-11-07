---
layout: post
title: Working Backwards
---

This week, we were tasked to analyze how issues were closed through accepted PRs. I decided to stick with [OpenStreetMap's iD editor](https://github.com/openstreetmap/iD) because I was able to see it in action during Hacktoberfest. I also chose issues hitting different parts of the codebase on purpose, so as to gain a broader overview of the project.

## Issue 1: [Add To and From fields to Route presets](https://github.com/openstreetmap/iD/issues/5408)
Fixed by [this PR](https://github.com/openstreetmap/iD/pull/5410), which is also what I referenced when I worked on a similar but simpler [issue](https://github.com/openstreetmap/iD/issues/5419) for Hacktoberfest. It's intimidating at first glance since the contributor changed over 20 files, but this is what I learned:

[First off](https://github.com/openstreetmap/iD/tree/master/data/presets#fields), "fields are reusable form elements that can be associated with presets" and are "defined in JSON files located under `data/presets/fields`." Thus, `data/presets/fields/to.json` and `data/presets/fields/from.json` were created. These also needed to be specified in `data/presets/fields.json`, which I believe is a collection of all fields. At this point, `to` and `from` are available but still not present in the Route presets. Thus, the contributor edited these (many) files (ex. `data/presets/presets/route/bus.json`) so they now include the newly created fields.


## Issue 2: [Help browser goes to the bottom of the next article, not top](https://github.com/openstreetmap/iD/issues/5439)
Fixed by [this PR](https://github.com/openstreetmap/iD/pull/5441), which goes to show how one line can drastically alter the UI. It's a simple fix but it took me a while to figure out the difference between `pane` and `content`. After playing around with both settings, I realized that `pane.property('scrollTop', 0)` doesn't work because the help pane is designed to fit within the height of the window. Thus, the pane itself is already in full view and the call to scroll up _the window_ is disregarded. `content` targets the actual articles which can be longer than the height of the pane, so the call to scroll up _the pane_ works as expected.


## Issue 3: [Adding a new point on a way should add a vertex, not a standalone point](https://github.com/openstreetmap/iD/issues/5409)
Fixed by [this PR](https://github.com/openstreetmap/iD/pull/5413). It was interesting that the reported issue was about a different aspect of editing the map (not being able to add a gate) which led to the discovery of an underlying bug: when a point was added to a way (ex. sidewalk), it was adding a pin-drop icon denoting a place (ex. cafe), not a vertex on that way.

This one was the most difficult to wrap my head around since it involves calls to various parts of the codebase. I'm still not sure if I understand the fix completely, but at a very high level, `addWay` creates a new node at the user's specified location (`node =  osmNode()`) and makes it the midpoint of two existing nodes on the way (`context.perform`). This addresses the issue because the the point is now connected to the other vertices instead of just being marked as a place separate from the way. Finally, `context.enter` changes the current instruction from `add-point` to `select`, so the user doesn't accidentally add more points than necessary and can continue browsing the map.
