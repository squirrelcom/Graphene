Interceptor Browser
===================
This is basically a text-based browser / search engine aggregator. It allows you to search the web through the terminal. It's still a work in progress,
but works decently well. The original inspiration for this project was Ranger File Manager, but after realizing that adapting it from local browsing to
the web would require a significant rewrite, I put the project aside. Then, a couple months later, I stumbled into FZF, and figured I'd give this tool
a try again using FZF instead of Ranger as a core. And this is the result.

Dependencies
=====
- FZF
- node.js
- (npm) puppeteer 
- Lynx

Usage
=====
```
interceptor [engine] [query]
```

Result: An FZF query with a set of links from the page classified as follows:
- golden/yellow: main group (probably results you meant to search for)
- white: regular group, if golden group is wrong, your results may be here
- black/gray: most-likely cruft (irrelevant/generic page links)
- cyan: navigational links that will result in query to be reperformed instead of opening the page

Selecting a result will open it in your browser of choice, unless the result is a navigational (cyan) link, which will re-trigger the search with new
offset.

If you want to add a new engine that I haven't included, look at an example of an existing engine in `engines` directory and customize it accordingly.
The only required fields are `query` (url used to formulate a search query) and `pager` (style/name hints the system uses to identify the pager - 
navigational link that goes to next page of search results). For best results, you should fill in as many parameters for the engine as possible.
If your engine works well, feel free to contribute it back to this repository.

FAQ
===

#### Will this work with any search engine/website?
Probably not, but it has worked with more than I expected, and will continue to improve.

#### How does it work? How does it know which groups are signfiicant and which is the main one?
It uses heuristics similar to what a human would do when navigating to a page. Groups that take up more visual space on the page are deemed more important.
Groups whose elements don't change at all between searches are deemed unimportant, groups whose names don't change but urls do are navigational (they apply
to the search in some way but aren't part of the results).

#### Can this be made faster/smarter by specifying the exact class/id of the results group?
That's typically what most scrapers do, and why they're easy to break with minor changes to the search engine. This aggregator uses more generic heuristics
and therefore harder to fool. For example, Google runs some sort of uglifier on their frontend. This uglifier mangles class/id names. These names then
stay consistent between searches (giving you the illusion of your selector working), but change every time Google redeploys their frontend (which happens
several times per week). This aggregator doesn't care about changes like that it analyzes link significance on the page the same way a human would. Moreover,
even if the engine decides to change the page in a significant way, the aggregator should be able to adapt to it after clearing your old cache.

#### Can this profile a page that's not a search engine?
In theory yes, in practice I haven't tried yet. You can pass an exact URL instead of the query to open, at which point the aggregator technically does not
need the query. You still need to specify an engine to use, however, to use for caching. If you have an idea for how to build a cache for a page that's not
a search engine, feel free to contribute to this project or open an issue. The main use of the cache is that it profiles cruft/navigational components.
Problem is a webpage that is not a search engine will not have the main results change between "searches" either, hence the caching logic will classify it
as cruft as well.
