jail
======

jail is an event script for the SimpleServer (a Minecraft Server tunnel) Events System which adds a jail commmand and functionality.


#### jail, by Xyvius

	This event provides a jail command "system" for the jailing and releasing of players who are being held for punishment or some other purpose. This requires multiple parts to work fully, but is relatively easy to implement, simply follow the SetUp instructions below.

##### Features:
	*Provides new commands for Mods/Admins as well as prisoners.
	*Self maintaining, Once you inprison a player the system takes care of the rest.
	*Automatically TPs players into the Cell specified.
	*Automatically reassigns player groups upon inprisonment and release.
	*Automatically releases prisoners once time is served.

##### Expertise required:
	
	This document assumes that you have at least a basic understanding of SimpleServer's config.xml and how its various sections are set up
and relate. If you set up your server using SimpleServer then you are probably fine, however if for some reason you find a part of this document
confusing or unclear, then by all means feel free to contact me.

##### Known Limits:
	There are some limitations that this jail system has which I feel need to be understood up front so that you don't waste time setting it up just to find that out that one of them keeps it from being a usefull tool for your situation.
	A: It requires a "prisoner group" to exist that all prisoners will be temporarily assign to during their sentence.
	B: It requires EVERY jail cell to be defined in the config.xml as an "area".
	C: It requires each cell to be occupied by no more then one(1) player at a time. (Even if that player is not online)
	   (NOTE: May actually be a way around this, however, I have not tested it, and don't want to advertise it quite yet)
	D: It requires the player being jailed to be in the same dimension as the cell to which they are being sent.

##### SetUp:
	In order to use this jail system all of its parts must be setup in the fashion described here. Please take the time to go over the SetUp fully, and be sure to follow each step.

	######1: Getting started... / Setting up a "Prisoner group"
    The First thing that needs to be done is to ensure that the "enableEvents" property in the "simpleserver.properties" file is set to "true". 	If it is not, then none of this code will ever execute.
		
	Next, is to set up a "group" or rank for prisoners in the config.xml under the groups section.
An example of how such a group might appear is below with an id of 0:
	```xml
	    <!-- Groups -->
		<group id="0" name="Prisoner" color="8" showTitle="true"/>
		<group id="1" name="Newbie" color="2"/>
		...
	```

######2: The Jail Cells
	The next thing that needs to be done is to have the area(s), building(s), and/or room(s) that will be used for "holding" prisoners(here forward referred to as "cells") already defined within the config.xml's "<area>...</area>" tags.  They can be stand alone areas or nested within other defined areas. Colors in the names are optional. The other arguments used (such as protections, etc. do not effect the jail system)
	Some Examples are as follows:
	
	A single stand alone cell (no color formatting):
	```xml
		<area name="Jail Cell" start="125,64,0" end="250,68,80"/>
	```		

	A nested group of cells (with color formatting):
	```xml
		<area name="�2Jail House�r" start="790,67,76" end="798,72,87">
			<area name="�1Cell 1�r" start="795,67,76" end="797,70,80"/>
			<area name="�1Cell 2�r" start="791,67,76" end="793,70,80"/>
		</area>
	```		
	*Note: This last example is what the rest of the document will refer to for examples.
	
	It is also important to note that you may have as many cells as you want, but for each cell there will be a group of variables that must be set up for the system to function properly.  They location of the cells within your world are irrelevent but must be in the same dimension as the player being jailed.
	
######3: The Event Code
	
	Next is to copy and paste all of the event code from the EVENT.md file into your SimpleServer's config.xml file. Don't worry about seperating the various elements (commands / events / etc.) as SimpleServer will parse and arrange those on its own upon loading/saving the config.xml file.
	
######4: The Commands

	Next is to set up the commands to work within your server setup. This system uses two(2) commands to function, they are as follows:
	/jail - This command is used by Admin / Staff to execute and maintain the jail system. Including adding and removing players.
	/jailtime - This command allows anyone currently serving time within the system to check how much time is left in their sentence.

	Start by finding the following lines which you just pasted into the config.xml file in the last step and adjust the "allow" arguments. If you have not saved and loaded the server since you pasted the code, then they are at the top of the code you just pasted, however, if you already have done so, then they will be in alphebetical order along with the other commands in the commands section.
	
	```xml
	<command name="jail" allow="4+" event="jailCmd"/>
	<command name="jailtime" allow="0" event="jailTime"/>
	```
	For the "/jail" command, set the "allow" argument to be the same as other Mod/Admin only commands.

	For the "/jailtime" command, set the "allow" argument to the same as the "Prisoner group" you created in step 1.
	
######5: The Variables
	#######5a: The Cell Variables
		
	As mentioned above, each cell has a set of variables that must be included in the config.xml. Simply copy the below set and make the	following changes for each Cell you wish to have included in the system.

	First, change the "#" symbol in each "name" field to the next available cell number.  DO NOT SKIP NUMBERS, as it may cause unexpected results to occur.
	
	Next, change the "value" of the "_desc" variable to be an EXACT match to the name of the associated Cell Name! If color codes are used	they must be included in this field as well. It is safest to simply copy/paste the info over to be sure it matches.
			
	Laslty, change the "value" field of the "_gps" variable to a CoOrdinate within the associated cell. This CoOrd. is where the player will be TPed to when entered into the Jail System.
		
	Leave the rest set to "null"
	
	```xml
	<event name="jail_cell#_desc" value="Exact Cell Name"/>
	<event name="jail_cell#_gps" value="X,Y,Z"/>
	<event name="jail_cell#_group" value="null"/>
	<event name="jail_cell#_player" value="null"/>
	<event name="jail_cell#_reason" value="null"/>
	<event name="jail_cell#_release" value="null"/>
	```
	*Note: You will find an initial dummy set for cell #1 already pasted into the config.xml from the steps above.
		
	An edited set of variables to match the above Example of Cells might look as follows:
	```xml
	<event name="jail_cell1_desc" value="�1Cell 1�r"/>
	<event name="jail_cell1_gps" value="796,68,78"/>
	<event name="jail_cell1_group" value="null"/>
	<event name="jail_cell1_player" value="null"/>
	<event name="jail_cell1_reason" value="null"/>
	<event name="jail_cell1_release" value="null"/>
	<event name="jail_cell2_desc" value="�1Cell 2�r"/>
	<event name="jail_cell2_gps" value="792,68,78"/>
	<event name="jail_cell2_group" value="null"/>
	<event name="jail_cell2_player" value="null"/>
	<event name="jail_cell2_reason" value="null"/>
	<event name="jail_cell2_release" value="null"/>
	```
	#######5b: The Other Variables
	There are three(3) more variables in addition to the "Cell Variables" listed above.  Of those, only two(2) need to be changed to suit your servers situation. You will find these already in the config.xml file near the bottom of what you pasted in earlier.
			
	First is the "jail_cells" variable, simply change the "value" to the total number of cells you have, thats it. EASY!!  Just be sure that the total you have are all represented by the "Cell variables" and that they are sequentially numbered skipping no numbers to avoid errors.
		
	Next, the "jail_group" variable, simply change its "value" to match the "id" number of the "Prisoner group" that you created above. Again, EASY!
			
	Laslty, the "jail_monitor" variable, if you stopped your server prior to editing this file it should always have a value of "false", however, if you are making changes to you config.xml file while your server is running and planning on "reload"ing the changes into memory the value of this variable will depend on the current state of the jail monitor event.
		
######6: Getting it rolling
	Getting the system to work is as easy as calling the jail_Monitor event from the built in onPlayerConnect event. (I preceed it with a sleep in order to delay the initial excecution of the Monitor so that the player is fully loaded before hand.) if you can't find an onPlayerConnect event already in your config.xml file simply add it.
	
	It might look something like:
	```xml
    	<event name="onPlayerConnect">
		sleep 1000
		launch jailMonitor
	</event>
	```
	
	Well, Thats it! Happy inprisoning! Remember if you need to contact me for anything feel free to do so using the below information, thanks!!
	
##### Contact Info:

	Name: Xyvius
	Website: www.forsakenlands.com
	Contact: http://www.forsakenlands.com/contact-us.html
	
