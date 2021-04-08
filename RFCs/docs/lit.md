# Title

## Subtitle

*italic*

**bold**

normal text

testing a request (simple http)

https://api.zotero.org/groups/2874623/collections/MMMN6KF9/items?key=DVEuQvXRt1hyJmM3Yn2vJgAK&limit=100&sort=title'

testing the whole thing


<!DOCTYPE html>
    <link rel="shortcut icon" href="favicon.ico" type="image/x-icon"/>
    <link rel="icon" href="favicon.ico" type="image/x-icon"/>
    <!-- GENERAL METADATA: -->
    <meta charset="utf-8" />
    <!-- character encoding -->
    <meta name="title" content="Collaborative Open Science Library - COS-Lib"/>

    <meta name="keywords" content="collaborative, library, libraries"/>
    <!-- keywords for search engines: each keyword separated by a comma (~10 is a good number) -->
    <meta name="description" content="a collaborative library"/>
    <!-- description of the web page -->
    <meta http-equiv="X-UA-Compatible" content="IE=edge"/>
    <!-- specifies a version of Internet Explorer -->
    <meta name="viewport" content="width=device-width, initial-scale=1"/>
    <!-- responsive -->

    <!-- DUBLIN CORE: -->
    <meta name="DC.title" content="Collaborative Open Science Library"/>
    <meta name="DC.contributor" content="Loraine Furter, Simon Worthington, Open Science Lab (OSL), TIB, HPG"/>
    <!-- person/organization responsible for making contributions to the content of the resource -->
    <meta name="DC.creator" content="Simon Worthington" />
    <!-- person/organization responsible for making the content of the resource -->
    <meta name="DC.publisher" content=""/>
    <!-- person/organization responsible for making the resource available -->
    <meta name="DC.subject" content="collaborative, library, libraries">
    <!-- comma separated (can be more than the keywords) -->
    <meta name="DC.description.abstract" content="a collaborative library"/>
    <meta name="DC.date" scheme="W3CDTF"  content="2017-12-19"/>
    <!-- date of creation or availability of the resource: yyyy-mm-dd -->
    <meta name="DC.type" content="webPage"/>
    <!-- = book / blogPost / artwork / bookSection / audioRecording / presentation (cf Zotero’s types)  -->
    <meta name="DC.format" content="text/html"/>
    <meta name="DC.identifier" content=""/>
    <!-- for instance: URL / URI / DOI (Digital Object Identifier) / ISBN / ISSN / … -->
    <meta name="DC.identifier.URL" content="http://url.net"/>
    <meta name="DC.language" content="eng"/>
    <!-- RFC3066 + ISO 639-3 language standard: http://en.wikipedia.org/wiki/ISO_639:a -->
    <meta name="DC.source" content="http://url.net/">
    <!-- link to a resource from which the present resource is derived -->
    <meta name="DC.relation" content=""/>
    <!-- reference to a related resource (using its identifier) -->
    <meta name="DC.rights" content=""/>
    <meta name="DC.bibliographicCitation" content="use a Chicago Manual of Style citation"/> <!-- doesn’t need editing -->

    <!-- FACEBOOK: -->
    <meta property="og:url" content="http://url.net/"/>
    <meta property="og:title" content="Collaborative Library"/>
    <meta property="og:image" content="shared-library.png"/>
    <!-- generic image: the best is 1200 x 630px for big images, or 100px by 100px -->
    <meta property="og:site_name" content="Collaborative Library"/>
    <meta property="og:description" content="a collaborative library"/>
    <meta property="og:type" content="webPage" />

<title>Collaborative Open Science Library - COS-Lib</title>
<link rel="stylesheet" href="css/style.css">
<link rel="stylesheet" href="fonts/webfont.css">

<meta name="viewport" content="width=device-width, initial-scale=1">

<script src="js/handlebars-1.0.rc.1.js"></script>
<script src="js/jquery-1.7.2.min.js"></script>

<!-- it takes the list that is called items in the JSON response (data) (to access it go to the url api.zotero.org/… bleow) and puts them in the template -->

<script id="entry-template" type="text/x-handlebars-template">
{{#each items}}
    <div class="one-item {{#if data.parentItem}} attached-note {{/if}} {{#if data.itemType.note}} independent-note {{/if}}">
        <!-- # is used to only output the data if they exist -->
        <div class="item-type">{{#if data.parentItem}} &#10549; <!--ATTACHED-<br />-->{{/if}}{{{ data.itemType }}}</div>
        <div class="item-reference"><em>{{ data.title }}</em>{{#if data.creators}}{{#each data.creators}}, {{ firstName }} {{ lastName }}{{/each}}{{/if}}{{#if data.publisher}}, {{ data.publisher }}{{/if}}{{#if data.date}}, {{ data.date }}{{/if}}</div>
        <div class="item-link">
            {{#if data.url}}<div class="item-tags">LINK </div> <a href="{{ data.url }}" target="_blank">{{ data.url }}</a>{{/if}}
        </div>
        <div class="item-tags">{{#if data.tags}}TAGS: {{#each data.tags}}</br>{{ tag }} {{/each}}{{/if}}</div>
        <div class="item-abstract">
            {{#if data.abstractNote}}<p class="abstract">Abstract: {{ data.abstractNote }}</p>{{/if}}
            <p>{{{ data.note }}}</p>
        </div>
        <!--<div class="card-hole">&nbsp;</div>-->
    </div>
{{/each}}
</script>



<script>
$(function() {
    /* takes the entry-template element */
    var source = $("#entry-template").html();
    /* the jquery library Handelbars converts the template text into a template object*/
    var template = Handlebars.compile(source);

/* TO ADD A TITLE WHEN THERE IS NO TITLE
(but it doesn’t change the way it is ordered…)
    var processData = function(inData) {
        var outData = [];
        for (var i=0; i < inData.length; i++) {
            item = inData[i];
            // do things with item
            if (!item.data.title) {
                item.data.title = "Here is a title!"
            }
            outData.push(item);
        }
        return outData;
    }
*/


var processData = function(inData) {
    var obj = {}
    for (var i=0; i < inData.length; i++) {
        item = inData[i];
        // do things with item
        // create a new way of ordering things, easier to use
        // (not as a list like before but as an ‘object’ ~ dictionnary)
        obj[item.data.key] = item;
    }
    //TO ORDER THINGS IN A SPECIFIC WAY
    var getSortString = function(item) {
        if (!item.data.title) {
            if (item.data.parentItem) {
                return obj[item.data.parentItem].data.title + item.data.note;
            } else {
                return item.data.note;
            }
        }
        return item.data.title;
    }
    //takes everytime two objects of the array and compares them
    inData.sort(function(a, b) {
        a = getSortString(a);
        b = getSortString(b);
        if (a === b) return 0;
        if (a < b) return -1;
        if (a > b) return 1;
    });
    return inData;
}


    /* gets the collection from zotero */
 $.getJSON('https://api.zotero.org/groups/2874623/collections/MMMN6KF9/items?key=DVEuQvXRt1hyJmM3Yn2vJgAK&limit=100&sort=title', function(incomingData) {
     /* if the query succeeds, the response will be available as the variable “data”: */
     /* K8F8FGKJ = os-library in Zotero group
     /* this variable is sent to the template above */
        var processedData = processData(incomingData);
        $("#items-container").html(template({ items: processedData }));
    });
});


</script>

<div class="container">
  <h1><a href="index.html">Collab_Lib &mdash; α prototype</a></h1>
  <h2>Research Software</h2>

  <div class="text-welcome">
      <p>&mdash; collaboratively maintained collection of software for research (using Zotero to make a curated list of research software, using tags, notes, etc.)</p>
  </div>

<div id="items-container">

</div>


    <!-- TEST OFFLINE


    <div class="one-item">
        <div class="item-type">note</div>
        <div class="item-reference"><em>_</em></div>
        <div class="item-tags">tag1</div>
        <div class="item-abstract"><p>Abstract: </p>
            <p>An independent note.
            This is the content of the note blabla bla…</p>
        </div>
        <div class="card-hole">●</div>
    </div>

    <div class="one-item">
        <div class="item-type">book</div>
        <div class="item-reference"><em>A Book Title</em>, First Name Last Name, Publisher, 2015</div>
        <div class="item-tags"><p>tag1</p><p>tag2</p></div>
        <div class="item-abstract"><p>Abstract: </p>
            <p> (?)</p>
        </div>
        <div class="card-hole">●</div>
    </div>

    <div class="one-item attached-note">
        <div class="item-type">note</div>
        <div class="item-reference"><em>_</em></div>
        <div class="item-tags">tag1</div>
        <div class="item-abstract"><p>Abstract: </p>
            <p>A note linked to the computer program item.
    The content blablabla…</p>
    <p>A note linked to the computer program item.
The content blablabla…</p>
<p>A note linked to the computer program item.
The content blablabla…</p>
<p>A note linked to the computer program item.
The content blablabla…</p>
        </div>
        <div class="card-hole">●</div>
    </div>
-->

</div>