**Introduction**

This is a collection of tips, tidbits and recipes on effective use of [OpenRefine](http://openrefine.org/), formerly known as Google Refine.  It's mainly my scrapbook and collection of things learned while working with the tool.

***Fetching URLs with OpenRefine***

This is a very powerful capability, useful for retrieving data from a REST service for augmenting datasets.

[Basic info here](https://github.com/OpenRefine/OpenRefine/wiki/Fetching-URLs-From-Web-Services)

If the REST service supports it, JSON is a useful output format to pull for OpenRefine, as OpenRefine has some excellent capabilities for parsing JSON

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

Let's deconstruct.  First concept:  Use the parseJson function.

>value.parseJson() 

will turn it into an object that OpenRefine can manipulate canonically.  It returns

>{"Results":{"FRSFacility":[{"CityName":"RICHMOND","Latitude83":"37.45093","LocationAddress":"5401 JEFFERSON DAVIS HWY","Longitude83":"-77.43415","RegistryId":"110000868918","CountyName":"CHESTERFIELD","FacilityName":"DUPONT SPRUANCE PLANT","ZipCode":"23234-2257","SupplementalLocation":null,"FIPSCode":"51041","StateAbbr":"VA"}]}}

No real surprise, thus far.

Let's go a level deeper.

>value.parseJson().Results

will unwrap the Results package, yielding

>{"FRSFacility":[{"CityName":"RICHMOND","Latitude83":"37.45093","LocationAddress":"5401 JEFFERSON DAVIS HWY","Longitude83":"-77.43415","RegistryId":"110000868918","CountyName":"CHESTERFIELD","FacilityName":"DUPONT SPRUANCE PLANT","ZipCode":"23234-2257","SupplementalLocation":null,"FIPSCode":"51041","StateAbbr":"VA"}]}

Let's see how many items there are in this package

>length(value.parseJson().Results.FRSFacility)

yields

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

>length(value.parseJson().Results.FRSFacility)

Yields

>2



