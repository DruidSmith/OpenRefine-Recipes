## Cleaning Addresses in OpenRefine

This is an experiment, to look at how addresses can be cleaned using OpenRefine.  Note that I am not saying "standardized" as that would mean adherence to a standard, such as the US postal standard.  This isn't as sophisticated as that - I'm basically looking to match addresses across disparate datasets.  I have tens of thousands of records to go through, and this may happen on a recurring basis, so I want an easy way to do it...  so enter OpenRefine to help do part of the heavy lift.

***Turn to upper case***

>toUppercase(value)

***Trim potential extraneous whitespace***

Use trim() to do this

>trim(toUppercase(value))

***Remove extraneous punctuation and other characters***

I'm not clever in the ways of regex...  regex is pure romulan to me.  There are probably other far more sophisticated ways do this than what I'm presenting, I'm open to suggestions - but this built-up method of doing it brute-force is working for the moment.

Use replace() to do this - append .replace("thing_to_be_replaced","replacement_value")

From my initial analysis of this dataset, I think I want to get rid of " . # / , ( ) and /t (tab) characters

Trying single quotes for getting rid of the doublequote value seems to work...

>trim(toUppercase(value)).replace('"','').replace(".","").replace("#","").replace(",","").replace("(","").replace(")","")

I want to replace the "/" with a " "

>trim(toUppercase(value)).replace('"','').replace(".","").replace("#","").replace(",","").replace("(","").replace(")","").replace("/"," ")

***Building GREL in succession***

To each piece, I will add on a new set of GREL directives

***Directionals***

I also want to have consistent directionals, so I'm going to append:

>.replace("EAST ","E ")
.replace("WEST ","W ")
.replace("NORTH ","N ")
.replace("SOUTH ","S ").replace(" SOU "," S ").replace(" SO "," S ")

***Street Names***

>.replace(" FIRST "," 1ST ")
.replace(" SECOND "," 2ND ")
.replace(" THIRD "," 3RD ")
.replace(" FOURTH "," 4TH ")
.replace(" FIFTH "," 5TH ")
.replace(" SIXTH "," 6TH ")
.replace(" SEVENTH "," 7TH ")
.replace(" EIGHTH "," 8TH ")
.replace(" NINTH "," 9TH ")

***Road Types***

Via a preliminary analysis I came up with a few observations 

"Street" had some occasional misspellings - "STEET", "SREET" et cetera - likewise, so did "Highway" - I will take the variants that I find, and plug them in as well.

>.replace(" AVENUE"," AVE")
.replace(" BUILDING"," BLDG").replace(" BYPASS"," BYP")
.replace(" BOULEVARD"," BLVD")
.replace(" CIRCLE"," CIR")
.replace(" COURT"," CT")
.replace(" COUNTY"," CTY")
.replace(" CTH"," CTY HWY")
.replace(" DRIVE"," DR").replace(" DIRVE"," DR")
.replace(" EXTENSION"," EXT")
.replace(" HIGHWAY"," HWY").replace(" HGWY"," HWY").replace(" HIGWAY"," HWY").replace(" HYW"," HWY")
.replace(" INDUSTRIAL"," INDL")
.replace(" INTERSECTION"," INT").replace(" INTEX"," INT").replace(" INTX"," INT")
.replace(" JUNCTION"," JCT")
.replace(" LANE"," LN")
.replace(" MM"," MI").replace(" MILE"," MI").replace(" MILES"," MI")
.replace(" ROAD"," RD").replace(" ROADS"," RD")
.replace(" ROUTE"," RT").replace(" RTE"," RT")
.replace(" STREET"," ST").replace(" SREET"," ST").replace(" STEET"," ST")
.replace(" USHWY"," US HWY")

Also, to handle a few other things in a consistent ways:

>.replace("&"," & ").replace("-"," - ").replace("  "," ")

Putting it all together:

>trim(toUppercase(value)).replace('"','').replace(".","").replace("#","").replace(",","").replace("(","").replace(")","").replace("/"," ").replace("EAST ","E ").replace("WEST ","W ").replace("NORTH ","N ").replace("SOUTH ","S ").replace(" SOU "," S ").replace(" SO "," S ").replace(" FIRST "," 1ST ").replace(" SECOND "," 2ND ").replace(" THIRD "," 3RD ").replace(" FOURTH "," 4TH ").replace(" FIFTH "," 5TH ").replace(" SIXTH "," 6TH ").replace(" SEVENTH "," 7TH ").replace(" EIGHTH "," 8TH ").replace(" NINTH "," 9TH ").replace(" AVENUE"," AVE").replace(" BUILDING"," BLDG").replace(" BYPASS"," BYP").replace(" BOULEVARD"," BLVD").replace(" CIRCLE"," CIR").replace(" COURT"," CT").replace(" COUNTY"," CTY").replace(" CTH"," CTY HWY").replace(" DRIVE"," DR").replace(" DIRVE"," DR").replace(" EXTENSION"," EXT").replace(" HIGHWAY"," HWY").replace(" HGWY"," HWY").replace(" HIGWAY"," HWY").replace(" HYW"," HWY").replace(" INDUSTRIAL"," INDL").replace(" INTERSECTION"," INT").replace(" INTEX"," INT").replace(" INTX"," INT").replace(" JUNCTION"," JCT").replace(" LANE"," LN").replace(" MM"," MI").replace(" MILE"," MI").replace(" MILES"," MI").replace(" ROAD"," RD").replace(" ROADS"," RD").replace(" ROUTE"," RT").replace(" RTE"," RT").replace(" STREET"," ST").replace(" SREET"," ST").replace(" STEET"," ST").replace(" TRAIL"," TRL").replace(" USHWY"," US HWY").replace("&"," & ").replace("-"," - ").replace("  "," ")

Next, you can compare data between columns:

>if(cells["A"].value == cells["B"].value, "similar", "different")

This just saved me from manually looking through thousands of records

As a future thing, I will try and look at more sophisticated methods for comparing, and for parsing and actually standardizing, but that's a bit beyond my scope and timeframe for getting things done on my current project.









