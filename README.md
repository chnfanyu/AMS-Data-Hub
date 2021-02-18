# GMNS-AMS Traffic Network Modeling 

## Introduction 

**GMNS**: The General Modeling Network Specification ([GMNS](https://github.com/zephyr-data-specs/GMNS)) defines a common human and machine-readable format for sharing routable road network files. It is designed to be used in multi-modal static and dynamic transportation planning and operations models.

**AMS**:  The objective of the [AMS](https://www.fhwa.dot.gov/publications/research/operations/13036/004.cfm) data hub is to define a prototype of operations and associated requirements that will allow for the effective integration of analysis modeling and simulation tools across various domains and scales. Easily exchange information and data at both the input and output levels.

The [ASU Trans + AI Lab](https://github.com/asu-trans-ai-lab) devotes to developing a better AMS data hub framework and a set of integrated open-source transportation tools. The objective of the repository is to integrate the developed software as a systematic modeling flow and demonstrate its successive steps, so call “7-steps modeling”. However, the modeling flow is not limited to seven steps. The prospective software under development are CGLite and Data2VDF for the traffic demand and supply calibration. The repository will continue to be updated. Meanwhile, the team also collaborates with other development teams to improve integration and modeling accuracy.


The continuous flow of GMNS-AMS modeling:

Step | Description | Software | Input Files | Output Files 
------------ | ------------- | ------------- | ------------- | -------------
0 | OSM data download| [Open Street Map(OSM)](https://www.openstreetmap.org/#map=14/47.6573/-122.3252) | - - | Map.osm  
1 | Convert OSM data to GMNS | [OSM2GMNS](https://github.com/asu-trans-ai-lab/OSM2GMNS) | Map.osm | Node.csv, Link.csv, poi.csv 
2 | Convert GTFS data to GMNS | [GTFS2GMNS](https://github.com/asu-trans-ai-lab/GTFS2GMNS) | Open transit data GTFS| Node.csv, Link.csv, poi.csv 
3 | Expand macroscopic network data to micro, meso | [net2cell](https://github.com/asu-trans-ai-lab/net2cell) | Node.csv, Link.csv | Meso and micro networks in node.csv and link.csv 
4 | Zone-to-zone travel demand | [grid2demand](https://github.com/asu-trans-ai-lab/grid2demand) | Node.csv, Link.csv, Poi.csv, Poi_trip_rate.csv | Demand.csv, Zone.csv, Accessibility.csv, Input_agent.csv 
5 | Traffic signal for timing | Signal2timing (In development), [Sigma-X](https://github.com/milan1981/Sigma-X) | Node.csv, Link.csv, Movement.csv | Timing.csv 
6 | AMS simulation | [A/B Street](https://github.com/dabreegster/abstreet), [DTALite](https://github.com/asu-trans-ai-lab/DTALite) | Demand.csv, Node.csv, Link.csv, Input_agent.csv | Agent.csv, Link_performance.csv 
7 | Visualization | [QGIS](https://www.qgis.org/en/site/), [NeXTA](https://github.com/asu-trans-ai-lab/NeXTA-GMNS) | Node.csv, Link.csv, Movement.csv, Zone.csv, Demand.csv | - - 




## Step 0:   Download Map File 
Open Street Map (OSM) provides a free editable map of the world, which accords with the ASU team's objective of developing open-source tools for traffic network modeling.
The open street website allows adjusting and directly downloading the OSM data file. The data file is the basic input for the GMNS-AMS modeling flow.

The interface of OSM:
![OSM](https://github.com/chnfanyu/GMNS-AMS/blob/main/img/University_District_OSM_Map.PNG)

## Step 1:  OSM2GMNS
Converting OSM data to [GMNS](https://github.com/zephyr-data-specs/GMNS) (General Modeling Network Specification) format is the first step of 7-steps modeling demonstration. The unified specification is cardinal for the effective integration of analysis in the GMNS-AMS modeling. The GMNS data is stored and represented in CSV file as the modeling input and output.

The outputs of OSM2GMNS (nodes, links and poi):
![nodes](https://github.com/chnfanyu/GMNS-AMS/blob/main/img/output.PNG)

## Step 2:  GTFS2GMNS
The public transit agencies publish the open public transit data in [GTFS](https://gtfs.org/) (General Transit Feed Specification) format.  Like OSM2GMNS, the step aims to convert the GTFS data for further application in the AMS data hub. The required files of GTFS2GMNS are stop.txt, route.txt, trip.txt, stop_times.txt in the GTFS data package.

## Step 3: net2cell
The net2cell is an open-source tool for expanding GMNS macro-network to mesoscopic and microscopic resolution. It aims to unify transportation modeling data in the same format for the more effective integration of analysis modeling and simulation across resolutions.

The analysis tools of different resolution networks are as follows: 
- Micro: These models are generally simulation-based and effective in replicating individual driver behavior, complex geometric configurations, and advanced features of traffic control devices.
- Macro: These include sketch-planning tools, regional/statewide TDMs, macroscopic simulation models, regional air quality models, freight models, etc.
- Meso: These include most DTA models and mesoscopic simulation models

The difference between macro and meso network: 

<img src="https://github.com/chnfanyu/GMNS-AMS/blob/main/img/Mesco.PNG" width="450" height="350"> Mesco
<img src="https://github.com/chnfanyu/GMNS-AMS/blob/main/img/Macro.PNG" width="450" height="350"> Marco


## Step 4: Grid2demand 
Grid2demand is a quick trip generation and distribution tool based on the trip generation and trip distribution methods of the standard 4-step travel model in transportation planning applications.

The four separate steps are described below.
- Trip Generation: Estimate how many trips entering or leaving a traffic-analysis-zone (TAZ)
- Trip Distribution: Estimate how many trips from each TAZ end in all TAZs through gravity model 
- Mode Choice: Estimate which travel-method is used (e.g., vehicle, transit, walk)
- Traffic Assignment: Distribute vehicles/traffic flow to different paths during travel

The grid2demand is the succeeding step of OSM2GMNS, which generates zone-to-zone travel demand based on alphanumeric grid zones. Users can obtain the demand file within a few lines of python code


The visualization of zone-to-zone demand volume over the University of Washington:
![demand](https://github.com/chnfanyu/GMNS-AMS/blob/main/img/Grid2demand_Demand%20Map.PNG)

## Step 5: Signal2timing	
***Quick Estimation Method (QEM)based implementation, GMNS based traffic signal API for multi-resolution modeling. This tool aims to automate the process of optimizing movement-based, phase-based signal control strategy and provide the interfaces for AMS modeling.***

The software package is still in development. The output is movement.csv and can be used in Sigma-X: an Excel-based Quick Estimation Method.

![QEM](https://github.com/xzhou99/Signal2timing/blob/master/doc/img/qem.png)


## Step 6: AMS Simulation

A mesoscopic DTA engine called DTALite can be run directly within NeXTA. DTALite is a mesoscopic simulation-assignment framework that uses a computationally simple but theoretically rigorous traffic queuing model in its simulation engine. The DTALite can be executed after obtaining the demand file from grid2demand.

The ASU Trans+AI Lab collaborates with the development team of A/B Street closely for improving the software efficiency.

A/B Street: 
![evaluating_impacts](https://github.com/chnfanyu/abstreet/blob/master/book/evaluating_impacts.gif)

## Step 7: Data Visualization.
Visualization is an increasingly important element of transportation analysis and is an integral part of any AMS data hub. The common visualization tools used in the 7 steps are QGIS and NeXTA

