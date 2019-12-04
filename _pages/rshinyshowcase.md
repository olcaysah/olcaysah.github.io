---
permalink: /RShiny/
title: RShiny Showcase
comments: false
sample_traffic_gallery:
- url: /assets/images/sampletraffic1.jpg
  image_path: /assets/images/sampletraffic1.jpg
  alt: "Density Snapshot"  
- url: /assets/images/sampletraffic2.jpg
  image_path: /assets/images/sampletraffic2.jpg
  alt: "User Locations Snapshot"  
- url: /assets/images/sampletraffic3.jpg
  image_path: /assets/images/sampletraffic3.jpg
  alt: "User Trajectories Snapshot"
overheight_gallery:
- url: /assets/images/OH1.jpg
  image_path: /assets/images/OH1.jpg
  alt: "Bar Plot View"  
- url: /assets/images/OH2.jpg
  image_path: /assets/images/OH2.jpg
  alt: "LiDAR Frame View"  
- url: /assets/images/OH3.jpg
  image_path: /assets/images/OH3.jpg
  alt: "2D and 3D LiDAR View"
classification_gallery:
- url: /assets/images/classification_tool.jpg
  image_path: /assets/images/classification_tool.jpg
  alt: "Vehicle Classification Tool"  
MCMIS_gallery:
- url: /assets/images/MCMIS.jpg
  image_path: /assets/images/MCMIS.jpg
  alt: "Vehicle Classification Tool"
---

I developed below tools as a helper for the project meetings or reporting. The tools are located in a private server that I developed for research purposes. I used RShiny to develop these tools. PostgreSQL database and Apache2 or NGINX web servers are used at the backend.

##	[Sample Traffic:](http://128.82.122.156:8888/shiny/tt/){:target="_blank"}
This project was about investigating relationship between driving patterns and traffic safety using smartphone GPS data. I developed the tool for data exploration purposes. This anonymous GPS data provided by a third party company.
The tool that I developed contains 3 tabs:
1.	Density Map: I created a 10x10 grid on the Hampton Roads area and count how many users pass through that grid. The size of the circles shows the density of where GPS data collected.
2.	User Locations: This map shows where the user opened the smartphone app. If you click on a marker on the map, you can see the anonymous information about the user and distance to major tunnels in the Hampton Roads area.
3.	User Trajectories: This map has same logic as user locations but it shows the where the app opened and closed with a polyline.

{% include gallery id="sample_traffic_gallery" %}

##	[Overheight Trucks:](http://128.82.122.156:8888/shiny/hrbt/){:target="_blank"}
This project was about the evaluation of strategies to reduce truck turnarounds at the Hampton Roads Bridge Tunnel (HRBT).
We installed a LIDAR scanner along with the surveillance cameras 1 mile before the tunnel entrance where VDOT over height beam sensor is located. We collected data for 36 days and analyzed the overheight trucks. In order to see the individual trucks video and LiDAR data I developed this interactive tool using RShiny.
The tool contains many features:
1. On the left panel you can filter the data by height and day and order by time or height. There is also some statistical information about the trucks.
2. On the main panel there are 2 tabs.

    2.1 Plot: You will see interactive bar plots that you can zoom in and out. Each bar is a truck. When you zoom in on the top of the bar you will see some information about the truck and link for the video of that truck.

    2.2 Cloud Points: When you click on a bar in the bar plot, if the bar color is change, LIDAR cloud point will be activated in this tab. You should click on the "Show Points" button to see the 3D cloud points of that truck and merged 2D points for truck profile.

{% include gallery id="overheight_gallery" %}


##	[Vehicle Classification Tool:](http://128.82.122.156:8888/shiny/classification/){:target="_blank"}
I developed a tool that ability to see each vehicle picture and lidar data. I developed an algorithm to extract images of the vehicle while in the LIDAR view area and also I developed an algorithm to merge each individual scan of lidar data for a single vehicle.
This tool also contains many features:  
1.	Left panel contains data selection and filtration.
2.	Middle panel contains vehicle picture and lidar points of that vehicle.
3.	Right panel contains selected vehicle information and editable vehicle characteristics.

{% include gallery id="classification_gallery" %}

## [Motor Carrier Management Information System:](http://mcmis.olcaysahin.com){:target="_blank"}
RShiny application for downloading and visualizing Motor Carrier Management Information System (MCMIS) data.
More information can be found here: [link](https://ask.fmcsa.dot.gov/app/mcmiscatalog/d_census_mcmis_doc){:target="_blank"}

This application can be seen under my porfolio's subdomain as: [http://mcmis.olcaysahin.com](http://mcmis.olcaysahin.com){:target="_blank"}

{% include gallery id="MCMIS_gallery" %}

**Disclaimer:** These tools originally developed by me (Olcay Sahin) and they used for reporting and data analysis purposes. Contain of the tools cannot be copied or used other than review purposes except MCMIS.
