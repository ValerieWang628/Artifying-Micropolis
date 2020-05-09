
# 💃🏻Artifying Micropolis (SimCity 1989) 

## About Micropolis

SimCity is also known as Micropolis, a city-building simulation video game developed by Will Wright. In January 2008, the SimCity source code was released under the free software GPL 3 license, renamed to Micropolis (the original working title) for trademark reasons.

👇🏻This is how Micropolis looked like. If you played it before, it is all coming back to you, right?
<p align="center">
<img src="https://static.wixstatic.com/media/5a3935_d8221f7659064f378b15e85b12904e74~mv2.png" width="600" height="407" />
</p>

## Project Overview
The Sims Series has been one of my all time favorites. 

As the original SimCity (1989) didn't have any type of arts or education in the systems, I decided to launch this two-week project to add a new type of building -- Museum into the game. The source code I used is the translated Micropolis source code in Java, forked from [David Culyba](https://github.com/dculyba) , who was my instructor for the course *Programming for Game Designers* at Carnegie Mellon University.

Let's "artify" the beloved city!


## Design Documentation
My primary design documentation is a one-page design chart that concisely concludes the implementation plan. This design approach is inspired by [Stone Librande's GDC talk on one-page design](https://www.gdcvault.com/play/1012356/One-Page).

In the one-page fashion, readers will be able to understand that particular core mechanic in the most intuitive way. As each mechanic is designed within an one-pager, a sophisticated game consists of multiple stacks of one-pagers. 

The complete PDF version of the one-page design is here: [Design Chart](https://github.com/ValerieWang628/pfgd-micropolis/blob/master/designDocForMuseums/FinalDesignDocForMuseum.pdf)

👇🏻Here is a snippet of the main design document:
<p align="center">
<img src="https://raw.githubusercontent.com/ValerieWang628/Micropolis-with-Museums/master/designDocForMuseums/FinalDesignDocForMuseumJPG.jpg" width="800" height="449" />
</p>

It can be easily seen from the design doc that adding a museum will involve three main aspects: 
1. Land value will increase
2. Crime rate will decrease
3. Education coverage will increase 
(also, there will be an education coverage map to let players know how impactful the museums are, and what regions are impacted)

The reasons why these three parts should be implemented will be expanded on in the section below.

## Design Flow

Having museums in the city means embracing education and arts. Beyond those, thinking of golden-standard museums such as the Musuem of Modern Art at NYC, good museums also bring about climbing land values, flow-in migration, subtle decrease of crime, and other socio-economic impact. To reflect this kind of change in the game, the system designer must be clearly aware of the internal relationships between different parameters, because the parameters out there are not all independent -- some affect others. 

For example, climbing land values might stimulate crime while the education impact museums bring might mitigate crime. On the other hand, climbing land values are supposed to push people away to other cities; however, like NYC, some people are willing to live close to MoMA despite the housing price. 

Thus, as Micropolis already has an established internally connected system as a whole, the addition of museum should only change the most directly related parameters, as these changed parameters will continue to affect other related ones. Land value, crime, and nearby education coverage are the three main directly affected metrics of the city. This is why I have chosen the implementation plan as shown in my design doc.

Adding one extra mechanic is not like simply squeezing some equations into the whole program. Instead, it is to introduce a whole new feature and to have it properly interact with the existing convoluted mechanics/systems.

## Implementation Results

Now, education coverage has been successfully embedded into the map overlays. If the player clicks the map, it will show the coverage of the museums. Also, as museums also affect population, land price, crime, and many other factors, players will be able to see how other parameters are affected in their respective maps.

👇🏻 Here is how the education map is embedded in the existing map system:
<p align="center">
<img src="https://raw.githubusercontent.com/ValerieWang628/Micropolis-with-Museums/master/designDocForMuseums/EducationCoverageMap.png" width="350" height="313" />
</p>

👇🏻 This is how the education coverage map looks like:
<p align="center">
<img src="https://raw.githubusercontent.com/ValerieWang628/Micropolis-with-Museums/master/designDocForMuseums/EducationRadiation.png" width="380" height="340" />
<img src="https://raw.githubusercontent.com/ValerieWang628/Micropolis-with-Museums/master/designDocForMuseums/EducationCoverage.png" width="380" height="340" />
</p>

☝🏻 The effect of the spread-out pixels on the maps are created using a smooth-out function which has a kernel that moves upon the map tile grids to make arithmetic changes so that the pixels are smoothly smudged.


## Implementation Details
To successfully add a museum mechanic into the SimCity, there are several important steps.


## 1. Graphics for museums -- the icon.

I decided that the museum should be the same size as the police station as well as the fire station (3 * 3 size).
And I later used Apple Preview to paint the icon (I would like to apologize for the incorrect shading of the building -- I was not familiar with any existing pixel drawing tool. Please bear with my limited toolset). 

👇🏻This is how the icons look like:
<p align="center">
<img src="https://raw.githubusercontent.com/ValerieWang628/Micropolis-with-Museums/master/designDocForMuseums/Pixel%20Icon%20for%20Museums.png" width ="400" height="210"/>
</p>


## 2. Register the museum as a new building type into the index system.

 #### 📍 Add new tile constants to the index dictionary
 
As Micropolis is 2D tile-based game, every different building type has a corresponding hardcoded index to the a particular sprite. These indexes are stored in a public class as tile constants.

For example,  dirt patches are indexed as 0 and 1; river sprites start from 3, etc. 
👇🏻This is the index "dictionary" containing 936 tiles in total, provided by [Chaim Gingold](https://escholarship.org/uc/item/8qr533m2):
<p align="center">
<img src="https://raw.githubusercontent.com/ValerieWang628/Micropolis-with-Museums/master/designDocForMuseums/956Tiles.PNG" width ="650" height="613"/>
</p>

👇🏻 This is the public class that stores tile constants:
It matches the spritesheet provided above. (Some indexes are slightly different from Gingold's chart due to this ported Java version.)
```java
    public class TileConstants{
	public static final short CLEAR = -1;
	public static final char DIRT = 0;
	static final char RIVER = 2;
	static final char REDGE = 3;
	static final char CHANNEL = 4;
	static final char RIVEDGE = 5;
	static final char FIRSTRIVEDGE = 5;
	static final char LASTRIVEDGE = 20;
	static final char TREEBASE = 21;
	static final char WOODS_LOW = TREEBASE;
	static final char WOODS = 37;
	static final char WOODS_HIGH = 39;
	static final char WOODS2 = 40;
	static final char WOODS5 = 43;
	static final char RUBBLE = 44;
	static final char LASTRUBBLE = 47;
	// the index goes to 936...
	}
```
Thus, to successfully register a museum into the system, I had to create a new index for the museum so the system will identity the building type when scanning all the tiles.👇🏻

	    static  final  char  MUSEUM  =  964;

 #### 📍 Import the museum sprites to the graphics library
 The museum sprite will be sliced to 9 pieces (3* 3), as one piece takes up one tile.
 
👇🏻Code Snippet: 
```rc
    # BEGIN MUSEUM #
	960 museum@0,0 (conducts)
	961 museum@16,0 (conducts)
	962 museum@32,0 (conducts)
	963 museum@0,16 (conducts)
	964 museum@16,16 (zone)(conducts)(building=3x3)(behavior=MUSEUM)(description=#28)
	965 museum@32,16 (conducts)
	966 museum@0,32 (conducts)
	967 museum@16,32 (conducts)
	968 museum@32,32 (conducts)
```
## 3. Develop the corresponding infrastructures for the museum.

Other than static index, adding museums requires following implementations:

 #### 📍 Update the utility function to specify the size and building cost of a museum

👇🏻Code Snippet:
```java
    public enum MicropolisTool{
		BULLDOZER(1, 1),
		WIRE(1, 5), //cost=25 for underwater
		ROADS(1, 10), //cost=50 for over water
		RAIL(1, 20), //cost=100 for underwater
		RESIDENTIAL(3, 100),
		COMMERCIAL(3, 100),
		INDUSTRIAL(3, 100),
		FIRE(3, 500),
		POLICE(3, 500),
		STADIUM(4, 5000),
		PARK(1, 10),
		SEAPORT(4, 3000),
		POWERPLANT(4, 3000),
		NUCLEAR(4, 5000),
		AIRPORT(6, 10000),
		MUSEUM(3, 1000),
		QUERY(1, 0);
	}
```
 #### 📍 Update the function to allow players to drag a museum to the map
 
👇🏻Code Snippet:
```java
    boolean apply1(ToolEffectIfc eff){
		switch (tool){
			case FIRE: return applyZone(eff, FIRESTATION);
			case POLICE: return applyZone(eff, POLICESTATION);
			case POWERPLANT: return applyZone(eff, POWERPLANT);
			case STADIUM: return applyZone(eff, STADIUM);
			case SEAPORT: return applyZone(eff, PORT);
			case NUCLEAR: return applyZone(eff, NUCLEAR);
			case AIRPORT: return applyZone(eff, AIRPORT);
			case MUSEUM: return applyZone(eff, MUSEUM);
			default:throw new Error("unexpected tool: "+tool);
		}
	}
```
#### 📍 Initialize musuem behaviors
👇🏻Code Snippet: (many other non-museum behaviors are removed for simplicity)
```java
    Map<String,TileBehavior> tileBehaviors;
    
	void initTileBehaviors(){
		HashMap<String,TileBehavior> bb;
		bb = new HashMap<String,TileBehavior>();
		bb.put("FIRE", new TerrainBehavior(this, TerrainBehavior.B.FIRE));
		bb.put("FLOOD", new TerrainBehavior(this, TerrainBehavior.B.FLOOD));
		bb.put("RADIOACTIVE", new TerrainBehavior(this, TerrainBehavior.B.RADIOACTIVE));
		bb.put("ROAD", new TerrainBehavior(this, TerrainBehavior.B.ROAD));
		bb.put("RAIL", new TerrainBehavior(this, TerrainBehavior.B.RAIL));
		bb.put("EXPLOSION", new TerrainBehavior(this, TerrainBehavior.B.EXPLOSION));
		bb.put("RESIDENTIAL", new MapScanner(this, MapScanner.B.RESIDENTIAL));
		bb.put("FIRESTATION", new MapScanner(this, MapScanner.B.FIRESTATION));
		bb.put("MUSEUM", new MapScanner(this, MapScanner.B.MUSEUM));
		this.tileBehaviors = bb;
	}
```	
## 4. Prepare for the stats change!	
 #### 📍 Add a function to define museum behaviors
 👇🏻Code Snippet:
```java
    void doMuseum(){
		boolean powerOn = checkZonePower();
		city.museumCount++;
		if ((city.cityTime % 8) == 0) {
			repairZone(MUSEUM, 3);
		}
		if (powerOn) {
			museumPopGrowth();
		}
	}
	
	void museumPopGrowth() {
		double rpop = city.resPop * 1.5;
		city.resPop = (int)Math.ceil(rpop);
		double cpop = city.comPop * 1.2;
		city.comPop = (int)Math.ceil(cpop);
		double ipop = city.indPop * 1.1;
		city.indPop = (int)Math.ceil(ipop);
	}
```	
 #### 📍 Add a function to apply museum behaviors that creates museum impacts
👇🏻Code Snippet: (many other non-museum behaviors are removed for simplicity)
```java
    public void apply(){
		switch (behavior) {
			case RESIDENTIAL:
				doResidential();
				return;
			case HOSPITAL_CHURCH:
				doHospitalChurch();
				return;
			case COMMERCIAL:
				doCommercial();
				return;
			case INDUSTRIAL:
				doIndustrial();
				return;
			case MUSEUM:
				doMuseum();
				return;
			default: assert false;
		}
	}
```	
	
## 5. Implement the real-time education coverage map.

 #### 📍 Update the function to store zone status
👇🏻Code Snippet:
```java
    public class ZoneStatus{
		/** Number from 0 to 27, identifying the type of building. */
		public int building;
		/** Number from 1 to 4, 1=Low, 2=Medium, 3=High, 4=Very High. */
		public int popDensity;
		/** Number from 5 to 8, 5=Slum, 6=Lower Class, etc. */
		public int landValue;
		/** Number from 9 to 12, 9=Safe, 10=Light, 11=Moderate, etc. */
		public int educationCoverage;
		public int crimeLevel;
		/** Number from 13 to 16, 13=None, 14=Moderate, 15=Heavy, etc. */
		public int pollution;
		/** Number from 17 to 20, 17=Declining, 18=Stable, etc. */
		public int growthRate;
	}
```	
 #### 📍 Add an education map overlay to the GUI
 👇🏻Code Snippet: 
 ```java
    public enum MapState{
		ALL, //ALMAP
		RESIDENTIAL, //REMAP
		COMMERCIAL, //COMAP
		INDUSTRIAL, //INMAP
		TRANSPORT, //RDMAP
		POPDEN_OVERLAY, //PDMAP
		GROWTHRATE_OVERLAY, //RGMAP
		LANDVALUE_OVERLAY, //LVMAP
		MUSEUM_EDUCATION_OVERLAY,
		CRIME_OVERLAY, //CRMAP
		POLLUTE_OVERLAY, //PLMAP
		TRAFFIC_OVERLAY, //TDMAP
		POWER_OVERLAY, //PRMAP
		FIRE_OVERLAY, //FIMAP
		POLICE_OVERLAY; //POMAP
	}
```
 #### 📍 Add a function to check the museum distribution
 👇🏻Code Snippet: 
 ```java
    void checkMuseum() {
		if (cityTime % 2 == 0) {
			MicropolisMessage z = null;
			if (museumCount - lastMuseumCount == 1) {
				z = MicropolisMessage.NEW_MUSEUM;
			}
			if (z != null) {
				sendMessage(z);
			}
			lastMuseumCount = museumCount;
		}
	}
```	    
	
#### 📍 Add a function to get the education impact from museums
👇🏻Code Snippet:
```java
    public int getEducationImpact(int xpos, int ypos) {
		if (testBounds(xpos, ypos)) {
			return museumMap[ypos/2][xpos/2];
		}
		else {
			return 0;
		}
	}
```	
#### 📍 Add a function to port and grid the education coverage map
👇🏻Code Snippet:
```java
    private void drawEducationMap(Graphics gr){
		int [][] A = engine.museumMap;
		for (int y = 0; y < A.length; y++) {
			for (int x = 0; x < A[y].length; x++) {
				maybeDrawRect(gr, getCI(10 + A[y][x]),x*6,y*6,6,6);
			}
		}
	}
```	
#### 📍 Add a function to color and render the education coverage map
👇🏻Code Snippet:
```java
    private int checkEducationOverlay(BufferedImage img, int xpos, int ypos, int tile){
		int e = engine.getEducationImpact(xpos, ypos);
		Color c = getCI(e);
		if (c == null) {
			return tile;
		}
		
		int pix = c.getRGB();
		for (int yy = 0; yy < TILE_HEIGHT; yy++) {
			for (int xx = 0; xx < TILE_WIDTH; xx++) {
				img.setRGB(xpos*TILE_WIDTH+xx,ypos*TILE_HEIGHT+yy,pix);
			}
		}
		return CLEAR;
	}
```
 #### 📍 Update the message system to notify players of the building of a new museum and the demand for museums
👇🏻Code Snippet: (many other non-museum messages are removed for simplicity)
```java
    public enum MicropolisMessage{
		NEED_RES, 
		NEED_COM, 
		NEED_IND, 
		NEED_ROADS, 
		NEED_RAILS, 
		NEED_POWER, 
		POP_2K_REACHED, 
		POP_10K_REACHED, 
		POP_50K_REACHED, 
		POP_100K_REACHED, 
		POP_500K_REACHED,
		NEW_MUSEUM,
		NEED_MUSEUM;
	}
```
<!---
Basically what I did is to add museum tiles into the whole program. For this part, it is rather similar to a police station. 
At the same time, this game is a grid-based game. It scans every tile at different frequencies. 

Thus, to make the museum influence the land value and crime which already have their own maps/grids, I will have to make a separate map for museum indicating the education impact museums bring. And make the museum map a smoothed-out kernel that moves above the land value map and crime map, adding/substracting the values on each tile to affect the land value and crime. --->

## Future Scope

I also would like to address the future scope here. SimCity is a very good practice for game designers to create new mechanics, despite the archaic convention of Java coding. I would like to add more interesting buildings in the future -- currently I am thinking of universities, elementary schools for the city education. Also, maybe adding a strip club could be interesting. 

And I have to admit that Micropolis is a fun game for not only players but also game developers.

