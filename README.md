# Adaptations of the original Micropolis
#### -- Adding museum as a new building into the SimCity

## Project Overview
This is a program that is based on the orginal Micropolis game/
Micropolis is based on the original SimCity from Electronic Arts / Maxis, and designed and written by Will Wright. 
The original Micropolis already has a well-formed system for electricity supply, traffic generation, pollution accumulation, city evaluation, and other important elements of SimCity.
However, as the early version does not have arts as an important aspect, I as the developer would like to add a museum feature here. Thus, this will be a two-week project that aims to implement the museum & education feature.

## Design Documentation
The primary design documentation is a one-page design chart that concisely concludes the implementation plan. 
-- This design approach is inspired by [Stone Librande's GDC talk on one-page design](https://www.gdcvault.com/play/1012356/One-Page).
Through one page, readers will be able to understand the core mechanism in the easiest way. And for the whole SimCity program where every mechanism can be conlcuded in one sheet, the entire program consists of multiple one-pagers.

The complete PDF version of the one-page design is here: [Design Chart]()

Here is a snippet of the main design document:
<p align="center">
<img src="https://github.com/ValerieWang628/pfgd-micropolis/blob/master/designDocForMuseums/FinalDesignDocForMuseumJPG.jpg" width="600" height="340" />
</p>

## Design Flow
Having museums in the city means having education and arts. Beyond those, thinking of golden-standard museums such as the Musuem of Modern Art at NYC, good museums also bring about climbing land values, flow-in migration, subtle decrease of crime, and other socio-economic impact. To reflect this kind of change in the game, the play designer must be clearly aware of the internal relationships between different parameters, because the parameters out there are not all dependent -- some affect others. For example, climbing land values might stimulate crime while the education impact museums bring might mitigate crime. On the other hand, climbing land values are supposed to push people away to other cities; however, like NYC, some people are willing to live close to MoMA despite the housing price. 

Adding one mechanism is not simply squeezing some equations into the whole program. Instead, it is to introduce a mechanism and have it properly interact with the existing convoluted mechanisms.

## Steps for implementation
To successfully add a museum mechanism into the SimCity, there are several important steps.

**First, the UI for museum -- that is, the pixelated icon.** 

I decided a museusm to be the same size as the police station as well as the fire station (3 * 3 size).
And I later used the Apple Preview to paint the icon.

This how the icons look like:
<p align="center">
<img src="https://github.com/ValerieWang628/pfgd-micropolis/blob/master/designDocForMuseums/Pixelated%20Icon%20for%20Museums.png" width ="600" height="316"/>
</p>

**Second, make a map.**

Basically what I did is to add museum tiles into the whole program. For this part, it is rather similar to a police station. 
At the same time, this game is a grid-based game. It scans every tile at different frequencies. 

Thus, to make the museum influence the land value and crime which already have their own maps/grids, I will have to make a separate map for museum indicating the education impact museums bring. And make the museum map a smoothed-out kernel that moves above the land value map and crime map, adding/substracting the values on each tile to affect the land value and crime. 

How the education map is embedded in the existing map system:
<p align="center">
<img src="https://github.com/ValerieWang628/pfgd-micropolis/blob/master/designDocForMuseums/EducationCoverageMap.png" width ="300" height="271"/>
</p>

How the education coverage looks like:
<p align="center">
<img src="https://github.com/ValerieWang628/pfgd-micropolis/blob/master/designDocForMuseums/EducationRadiation.png" width ="300" height="271"/>
</p>

## Design Reflection

Most of my reflection is reflected in the [Postmortem](https://github.com/ValerieWang628/pfgd-micropolis/blob/master/designDocForMuseums/Postmortem%20for%20museum%20design.pdf). In this document, I listed three things went wrong and right respectively during my implementation. Overall, this is a successful museum implementation.

I also would like to address the future scope here. SimCity is a very good program for game designers to create new mechanisms, despite the archaic convention of Java coding. I would like to add more interesting buildings in the future. 

I have to admit, Micropolis is a fun game for not only players but also game developers.

