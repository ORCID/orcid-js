ORCID-JS
========

A client side reference list generator for ORCID records
--------------------------------------------------------

This library can be used to generate on the fly reference lists from ORCID records.  It also exposes the entire [V2.0_rc1 Public ORCID API](https://pub.orcid.org/v2.0_rc1) via a swagger client (including the stats API).  It can be used to generate any CSL style, Citeproc-JSON or bibtex.

Hello world example
-------------------
```javascript
	//write a reference list to the page in the default style (chicago)
	//fetches citations asynchronously, so order is not guarenteed
	orcid.init(function(){
		orcid.resolveCitations("0000-0003-0902-4386", function(citations, failures){
			for (c in citations){
				$("body").append(citations[c] +"<br/>");	
			}
			for (f in failures){
				$("body").append(failures[f].title + " (" + failures[f].citeprocURL+") FAILED to fetch metadata<br/>");		
			}
		});		
	});
```

Viewing the examples locally
----------------------------
Chrome and Firefox will prevent the browser from loading other resources from the file system.  To test locally, you need to server the files from a webserver.  This can easily be done using python.  Run the following command:

	python -m SimpleHTTPServer

Then access the example at http://localhost:8000/example.html

Where does the metadata come from?
-----
To generate citations, the library does the following:

*	Obtain a public list of activites from an ORCID record
*	For each work fetch authoritative citation metadata available using the **prefered source**
*  For works with DOIs, citeproc metadata is obtained directly from Crossref or Datacite
*  For other works, citeproc is obtained from the ORCID registry.  If there is a BibTeX citation, this is transformed into Citeproc-JSON, otherwise ORCID work metadata is used
*	For each work, attempt to create a URL based on the idenfifier
*	Apply a [CSL citation style](http://citationstyles.org/) to generate reference list or BibTeX

As other services add the ability to resolve citeproc metadata, this tool will be updated to use it.

Usage
-----
The most useful method has this signature:

	function resolveCitationsFromORCIDID(orcidID, callback, oneByOne, optionalCitationStyle, returnCiteprocObjects)

There is also an [example.html](https://github.com/TomDemeranville/orcid-js/blob/master/example.html) page

```javascript
	orcid.init(callback);
	function callback(){
		//to fetch asyncronously and have the callback called once for each citation (recommended) use:
		orcid.resolveCitations("0000-0000-0000-0000", onCompleteCallback, true);
		//to get the citations an array of citation strings via a callback as a batch use:
		orcid.resolveCitations("0000-0000-0000-0000", onCompleteCallback, false);
		// the default style is Chicago, we can ask for bibtex instead like this:
		orcid.resolveCitations("0000-0000-0000-0000", onCompleteCallback, false, orcid.styleBibtex);
		// if you want an array of citeproc JSON objects instead, use this:
		orcid.resolveCitations("0000-0000-0000-0000", onCompleteCallback, true, "", true);	
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

If you would like to load a citation format that is not supplied by default (APA, Chicago, Bibtex) then use the styles.js helper to fetch it from the server.  All the styles in the style repo should be available.  Something like this:

```javascript
	CSLStyles.fetchStyleXML("academy-of-management-review", function(styleXML){
		orcid.resolveCitations(orcidID, updatePage, true, styleXML);		
	});
```

Dependencies
------------
swagger-client.js, jquery, citeproc-js 
transitive: xmle4x.js,xmldom.js

When cloning this repository, you must run

	git submodule init
	git submodule update

To fetch the dependancies

Issues
------
Chrome always logs the following to the console:

	xmle4x.js:11 Uncaught SyntaxError: Unexpected token default

This is a citeproc dependency used by legacy browsers and does not affect function in Chrome/Firefox.
