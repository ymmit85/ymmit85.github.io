---
layout: post
title: Apply SNOW Template via Scripted REST API
date: 2016-12-22 20:36
author: ymmit85
comments: true
categories: [Automation, Posts, SNOW, vRA, vRO]
---
While getting some workflows built up and running in vRA/vRO recently we wanted to have changes/requests/incidents logged during the workflow to save going over to SNOW to log them.

If you haven't seen it ServiceNow has a really good <a href="https://developer.servicenow.com/app.do#!/home" target="_blank">developer program </a>where you can spin up personal instances of SNOW and do whatever you like to test this type of stuff out if you don't have access to instances at work.

Once you get familiar with the SNOW API you will see it's pretty straightforward to create a new incident/request/change, but applying a template already created in SNOW isn't possible with the normal API. Here is an example going through Postman.

<img class="alignnone size-full wp-image-292" src="https://ymmitsblog.files.wordpress.com/2016/12/snow1.jpg" alt="snow1" width="785" height="880" />

Now the reason I'm going with templates in SNOW is the client will be managing the content including assignments etc, whereas if you were to do it all via vRO it would take quite a bit of time and opens up more possibility for issues.

First thing to do will be to log into SNOW and go into System Web Services - Scripted REST APIs, create a new API. Give your API a name and submit it.

<img class="alignnone size-full wp-image-293" src="https://ymmitsblog.files.wordpress.com/2016/12/snow2.jpg" alt="snow2" width="576" height="438" />

<img class="alignnone size-full wp-image-294" src="https://ymmitsblog.files.wordpress.com/2016/12/snow3.jpg" alt="snow3" width="1457" height="303" />

Create two Request Headers, in this example and for the code I'll be using <em>tableName</em> and <em>templateName.</em>

<img class="alignnone size-full wp-image-295" src="https://ymmitsblog.files.wordpress.com/2016/12/snow4.jpg" alt="snow4" width="880" height="257" />

Create a new Resource, to keep things simple I called it the same as the API Record from Template. In this resource set the HTTP method to PUT (or PUSH, SNOW treats these the same) and copy in the following JavaScript code.

<img class="alignnone size-full wp-image-296" src="https://ymmitsblog.files.wordpress.com/2016/12/snow5.jpg" alt="snow5" width="1449" height="901" />
<pre class="brush:jscript">(function process(/*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {

// implement resource here

var templateName = request.getHeader('templateName');
var tableName = request.getHeader('tableName');
if (!templateName)
{
//If templateName header was not set or was malformed, return an error.
response.setStatus(417);
return 'Unable to process request. Template header \'templateName\' not defined. ' + 'Please define a request header called \'templateName\'';
}
if (!tableName)
{
//If templateName header was not set or was malformed, return an error.
response.setStatus(417);
return 'Unable to process request. Table header \'templateName\' not defined. ' + 'Please define a request header called \'tableName\'';
}

var res = {};
templateName = templateName.trim();
var record = new GlideRecord(tableName);
record.initialize();
record.applyTemplate(templateName);
var id = record.insert();
res['record_sys_id'] = id;
return res;
})(request, response);
</pre>
This script will validate that a value has been entered in for both our Headers, if not an error is thrown. Next a new record is created on the table, the template is applied committed. Finally the SNOW sys_id is returned. You can then use this sys_id in other workflows to update the record with the remaining required fields or close off the record.

To validate this has worked, get the name of a template in SNOW and check which table it is for, you can this by going to System &gt; Templates, or via the API (note the SNOW REST API Explorer wont let you select this table, you need to do it from Postman or something similar).

<img class="alignnone size-full wp-image-313" src="https://ymmitsblog.files.wordpress.com/2016/12/snow91.jpg" alt="snow9" width="473" height="53" />

<img class="alignnone size-full wp-image-297" src="https://ymmitsblog.files.wordpress.com/2016/12/snow6.jpg" alt="snow6" width="1189" height="387" />

<img class="alignnone size-full wp-image-298" src="https://ymmitsblog.files.wordpress.com/2016/12/snow7.jpg" alt="snow7" width="1456" height="695" />

Submit the request and by any luck you should get a 200 Status code and a record_sys_id value. Check in the associated table in SNOW and you will see the new record with template applied.

<img class="alignnone size-full wp-image-299" src="https://ymmitsblog.files.wordpress.com/2016/12/snow8.jpg" alt="snow8" width="671" height="540" />
