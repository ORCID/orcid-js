ORCID-JS
========

A client side reference list generator for ORCID records
--------------------------------------------------------

This library can be used to generate on the fly reference lists from ORCID records.  It also exposes the entire V2.0_rc1 Public ORCID API via a swagger client (including the stats API).  It can be used to generate any CSL style, Citeproc-JSON or bibtex.

Usage
=====
There is also an example.html page

	orcid.init(callback);
	var orcidID = 0000-0000-0000-0000;//your orcid here!
	function callback(){
		//to get an array of citation strings via a callback use:
		orcid.resolveCitations(orcidID, onCompleteCallback);
		// the default style is Chicago, we can ask for bibtex instead like this:
		orcid.resolveCitations(orcidID, onCompleteCallback, orcid.styleBibtex);
		// if you want an array of citeproc objects instead, use this:
		orcid.resolveCiteprocObjects(orcidID, onCompleteCallback);	
	}

	//If all you need is a list of titles, identifiers and URLS, use simpleWorks
	//This also demonstrates how to access the API via swagger (full v2.0 api is supported)
	var client = orcid.init(callback);
	function callback(){
		client.apis["Public API v2.0_rc1"].viewActivities({orcid:orcidID}, function(data) {
			var simpleWorks = orcid.activitiesToSimpleWorks(data);
			//do whatever with the simpleWorks
		});
	}

Dependencies
============
swagger-client.js, jquery, citeproc-js [transitive xmle4x.js,xmldom.js]

