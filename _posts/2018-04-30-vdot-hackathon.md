---
layout: archive
title: "VDOT Hackathon"
modified: 2018-04-30
comments: false
share: true
---

	
* [VDOT SmarterRoads Hackaton](https://smarter-roads-hackathon-vb.devpost.com/)

	Old Dominion University's Transportation Research Institute (TRI) attended the VDOT SmarterRoads Hackathon. We formed 3 teams and we have succesfully accomplished the projects in a given short time. 

	Me and my colleague Gulsevi developed a [web application of toll-based route guidance](https://devpost.com/software/web-application-of-toll-based-route-guidance){:target="_blank"}. Toll data has been parsed real time from [SmarterRoads Data Portal](http://smarterroads.org){:target="_blank"}, [travel time](https://developers.google.com/maps/documentation/distance-matrix/intro){:target="_blank"} and [direction](https://developers.google.com/maps/documentation/directions/intro){:target="_blank"} data parsed from [Google Maps Api](https://cloud.google.com/maps-platform/){:target="_blank"}. 

	When the user enter origin and destination locations, parsed data analyzed and suggests multiple route options with the tolling information. User can select desired route option for the destination.

	The web application written in R with shiny package The source code can be found in [here](https://github.com/olcaysah/VDOT_Hackathon){:target="_blank"}. Leaflet map has been used for displaying the waypoints of selected route.

	The other teams projects are [SmartPave](https://devpost.com/software/smartpave-smart-systematic-pavement-management){:target="_blank"} and [Vi-Care](https://devpost.com/software/vi-care){:target="_blank"}.

	Unfortunately my project was not selected but I had a great time and experience.

	In the hackathon, based on the submission rules, we used only provided data feeds. Actually there is a plenty of resources for the data feeds. The list is [here](https://docs.google.com/document/d/14WyrbsxvVkHfzruC_mCqdrKWr8J-5H-xzsy5mDcR650/edit){:target="_blank"}. Download it before it has gone!

	Here is the snapshot of the user interface. I want to note that it needs be still improved. 

	![alt text](http://olcaysahin.com/pix/snapshot.PNG "Toll based route choise user interface")