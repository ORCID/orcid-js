ORCID-JS
========

A client side reference list generator for ORCID records
--------------------------------------------------------

This library can be used to generate on the fly reference lists from ORCID records.  It also exposes the entire [V2.0_rc1 Public ORCID API](https://pub.orcid.org/v2.0_rc1) via a swagger client (including the stats API).  It can be used to generate any CSL style, Citeproc-JSON or bibtex.

The general flow is:
*	Obtain a list of activites from an ORCID record
*	Filter and simplify to extract just works
*	For each work, attempt to create a URL based on the idenfifier
*	For each work, fetch the most authoritative source of citation metadata available (currently DOI or ORCID)
*	Apply CSL to generate reference list or bibtex

Usage
-----
There is also an [example.html](https://github.com/TomDemeranville/orcid-js/blob/master/example.html) page

```javascript
	orcid.init(callback);
	function callback(){
		//to get an array of citation strings via a callback use:
		orcid.resolveCitations("0000-0000-0000-0000", onCompleteCallback);
		// the default style is Chicago, we can ask for bibtex instead like this:
		orcid.resolveCitations("0000-0000-0000-0000", onCompleteCallback, orcid.styleBibtex);
		// if you want an array of citeproc objects instead, use this:
		orcid.resolveCiteprocObjects("0000-0000-0000-0000", onCompleteCallback);	
	}

	//If all you need is a list of titles, identifiers and URLS, use simpleWorks
	//This also demonstrates how to access the API via swagger (full v2.0 api is supported)
	orcid.init(callback);
	function callback(){
		orcid.client.apis["Public API v2.0_rc1"].viewActivities({orcid:"0000-0000-0000-0000"}, function(data) {
			var simpleWorks = orcid.activitiesToSimpleWorks(data);
			//do whatever with the simpleWorks
		});
	}
```

Dependencies
------------
swagger-client.js, jquery, citeproc-js 
transitive: xmle4x.js,xmldom.js

Notes
-----
Chrome always logs the following to the console:

	xmle4x.js:11 Uncaught SyntaxError: Unexpected token default

This is a citeproc dependency used by legacy browsers and does not affect function in Chrome/Firefox.