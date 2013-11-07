Calling REST services and parsing JSON with OpenRefine
-----------------------------

**Introduction**

This is part of what will hopefully become a budding collection of tips, tidbits and recipes on effective use of [OpenRefine](http://openrefine.org/), formerly known as Google Refine.  It's mainly my scrapbook and collection of things learned while working with the tool.

**Calling Web Services and Retrieving JSON with OpenRefine**

This is a very powerful capability, useful for retrieving data from a REST service for augmenting datasets.

Basically, the process is to construct a URL that can be passed into a REST service call.  In OpenRefine, you can do this using "Edit Column" -> "Add Column by Fetching URLs"

The process is straightforward, an easy way is to just concatenate the pieces you need for the URL call.  In my case, I'm going to be calling my own [facility lookup service](http://www.epa.gov/enviro/html/fii/FRS_REST_Services.html) using "City", "State" and "Name" fields from a spreadsheet, in GREL it's done with cells["ColumnName"] and other pieces needed to construct the URL in quotes concatenated via '+':

>"http://ofmpub.epa.gov/enviro/frs_rest_services.get_facilities?city_name="+cells["City"].value+"&state_abbr="+cells["State"].value+"&facility_name="+cells["Name"].value+"&output=JSON"

When first preparing the service call, you may want to first test on a small subset of the data, like 10 records to test your call logic.  You may want to keep a notepad handy on your desktop for pasting things while testing.  I use EverNote to keep code snippets and notes organized while working - your mileage may vary.

Also, when setting up the calls, there is a value, "throttle delay" - this sets up a delay between calls in milliseconds.  This is for a couple of reasons- services may not respond in as timely a fashion as one might like, resulting in timeouts - also, server admins might not appreciate thousands of service calls all trying to hit a server in rapid succession (it would basically look like a Denial of Service attack).  Don't be greedy, no doubt other folks also need to use the servers.  It looks like OpenRefine wants a minimum of 1000 milliseconds (1 second) between calls.  I usually set a reasonable threshold, and set it up to run overnight.

In OpenRefine, it will do all of the service calls before populating and refreshing the page.  This may take some time, so bear that in mind if you have a lot of records to work with.

[Basic info on fetching URLs is available  here](https://github.com/OpenRefine/OpenRefine/wiki/Fetching-URLs-From-Web-Services)

***JSON***

JSON is ["JavaScript Object Notation"](http://www.json.org/) - originally developed for being easy to work with from JavaScript but it's actually a very versatile little format, usable for many other types of applications as well, similar to XML but generally easier to work with on the web.  

If the REST service you are calling supports it, JSON is a very useful output format to pull for OpenRefine, as OpenRefine has some excellent capabilities for parsing JSON.  Here is where I had to learn more via trial and error.

Suppose the REST service returns a response like this:
>{
"Results":{
"FRSFacility":[
{
"RegistryId":"110000868918",
"FacilityName":"DUPONT SPRUANCE PLANT",
"LocationAddress":"5401 JEFFERSON DAVIS HWY",
"SupplementalLocation":null,
"CityName":"RICHMOND",
"CountyName":"CHESTERFIELD",
"StateAbbr":"VA",
"ZipCode":"23234-2257",
"FIPSCode":"51041",
"Latitude83":"37.45093",
"Longitude83":"-77.43415"
}
]
}
}

The top level is Results, within that, there is FRSFacility, which in turn contains a bunch of attributes and values

***Deconstructing a JSON payload***

Let's deconstruct.  First concept:  Use the parseJson function.

>value.parseJson() 

will turn it into an object that OpenRefine can manipulate canonically.  It returns

>{"Results":{"FRSFacility":[{"CityName":"RICHMOND","Latitude83":"37.45093","LocationAddress":"5401 JEFFERSON DAVIS HWY","Longitude83":"-77.43415","RegistryId":"110000868918","CountyName":"CHESTERFIELD","FacilityName":"DUPONT SPRUANCE PLANT","ZipCode":"23234-2257","SupplementalLocation":null,"FIPSCode":"51041","StateAbbr":"VA"}]}}

No real surprise, thus far.

Let's go a level deeper.

>value.parseJson().Results

will unwrap the "Results" package, yielding

>{"FRSFacility":[{"CityName":"RICHMOND","Latitude83":"37.45093","LocationAddress":"5401 JEFFERSON DAVIS HWY","Longitude83":"-77.43415","RegistryId":"110000868918","CountyName":"CHESTERFIELD","FacilityName":"DUPONT SPRUANCE PLANT","ZipCode":"23234-2257","SupplementalLocation":null,"FIPSCode":"51041","StateAbbr":"VA"}]}

In this case, "FRSFacility" is the item that we want to get at

***Beginning to navigate the JSON payload***

Let's see how many items there are in this package

>length(value.parseJson().Results.FRSFacility)

yields a count of how many "FRSFacility" items there are inside Results

>1

Let's test that, by trying it on a bigger JSON payload

>{
"Results":{
"FRSFacility":[
{
"RegistryId":"110035806982",
"FacilityName":"DUPONT",
"LocationAddress":"5115 SOUTH COMMERCE ROAD",
"SupplementalLocation":null,
"CityName":"RICHMOND",
"CountyName":"CHESTERFIELD",
"StateAbbr":"VA",
"ZipCode":"23234",
"FIPSCode":"51041",
"Latitude83":"37.45369",
"Longitude83":"-77.42649"
}
,{
"RegistryId":"110000868918",
"FacilityName":"DUPONT SPRUANCE PLANT",
"LocationAddress":"5401 JEFFERSON DAVIS HWY",
"SupplementalLocation":null,
"CityName":"RICHMOND",
"CountyName":"CHESTERFIELD",
"StateAbbr":"VA",
"ZipCode":"23234-2257",
"FIPSCode":"51041",
"Latitude83":"37.45093",
"Longitude83":"-77.43415"
}
]
}
}

Now, we see that

>length(value.parseJson().Results.FRSFacility)

Yields

>2

FRSFacility items

***Accessing items within the payload***

Let's try to get at one of the items in this second payload

>value.parseJson().Results.FRSFacility

shows the two FRSFacility items, we want the first one.  It's as simple as

>value.parseJson().Results.FRSFacility[0]

which will get the first one:

>{"CityName":"RICHMOND","Latitude83":"37.45369","LocationAddress":"5115 SOUTH COMMERCE ROAD","Longitude83":"-77.42649","RegistryId":"110035806982","CountyName":"CHESTERFIELD","FacilityName":"DUPONT","ZipCode":"23234","SupplementalLocation":null,"FIPSCode":"51041","StateAbbr":"VA"}

Let's test this approach to see if we can get the second one:

>value.parseJson().Results.FRSFacility[1]

And, lo and behold, we get the second one:

>{"CityName":"RICHMOND","Latitude83":"37.45093","LocationAddress":"5401 JEFFERSON DAVIS HWY","Longitude83":"-77.43415","RegistryId":"110000868918","CountyName":"CHESTERFIELD","FacilityName":"DUPONT SPRUANCE PLANT","ZipCode":"23234-2257","SupplementalLocation":null,"FIPSCode":"51041","StateAbbr":"VA"}

***Accessing attribute values within the item***

Now, let's try and access some of the attribute data associated with the item.  That's as simple as appending the attribute name.  Let's try and get the RegistryID for that second facility:

>value.parseJson().Results.FRSFacility[1].RegistryId

yields

>110000868918

Voila.







