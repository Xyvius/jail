# jail
### Version: 1.0.0
### By: Xyvius
### Updated: 2.24.14
### jail is a SimpleServer Event Script which adds in a "jail system".

```xml
    <command name="jail" allow="4+" event="jailCmd"/>
    <command name="jailtime" allow="0" event="jailTime"/>
    <event name="jailAdd">
	rem Init Vars
		set '#target '#cell '#time '#reason to #POP Aexplode
		set '#id 0
		set '#count 1
	rem If a SUB then make value Neg.
		if #cell "sub" eq
			set '#time #time -1 *
		endif		
	rem ID target's cell
		while #count $jail_cells 1 + lt
			set '#player '$jail_cell #count . "_player" .
			if #target #player evalvar eq
				set '#id #count
			endif
		inc '#count
		endwhile
	rem Execute to discovered cell if applicable.
		if #id 0 eq
			say #PLAYER "§4Invalid Target! §e(Check Capitalization)§r"
		else
			set '#release '$jail_cell #id . "_release" .
			set '#release #release evalvar
			if #release null eq
				set '#release currtime
			endif
			set '$jail_cell #id . "_release" . #time 60000 * #release +
			say #PLAYER "§eTime Updated for:" #target ..
		endif
	</event>
    <event name="jailCheck">
	rem Init Vars
		set '#msg "Jail Cell Check:" #PLAYER ..
		set '#players getplayers
		set '#count 1
	rem Display Check string in Console
		print #msg
	rem Loop for each cell check
		while #count $jail_cells 1 + lt
			set '#cell '$jail_cell #count . "_desc" . evalvar
			set '#player '$jail_cell #count . "_player" . evalvar
			set '#gps '$jail_cell #count . "_gps" . evalvar
			set '#release '$jail_cell #count . "_release" . evalvar currtime -
		rem If prisoner is online execute checks
			if #players #player Acontains true eq
				if #player #cell isinarea
					set '#msg #player "in Cell" ..
					rem #count .. "." .
					print #msg
					if #release 0 lt
						run jailFree #player
					else
					endif
				else
					set '#msg #player "NOT in cell!" ..
					print #msg
					say #player "§4VIOLATION§1: §eBeing outside of cell§r"
					say #player "§4PENALTY§1: §e1 Hour additional sentence§r"
					run jailAdd #player add 60
					teleport #player #gps
					endif
				endif
			endif
			inc '#count
		endwhile
	rem Disp Console complete msg
		set '#msg "Jail Cell Check Complete:" #PLAYER ..
		print #msg
	</event>
    <event name="jailCmd">
	rem Init Vars
		set '#target '#cell '#time '#reason to #POP Aexplode
		set '#players getplayers
		set '#playergroup #PLAYER getgroup
		set '#targetgroup #target getgroup
		set '#showhelp false
	rem Set About equal inputs ...
		if #target "ABOUT" eq
			set '#target "about"
		endif
		if #target "About" eq
			set '#target "about"
		endif
	rem Set Add equal inputs ...
		if #cell "ADD" eq
			set '#cell "add"
		endif
		if #cell "Add" eq
			set '#cell "add"
		endif
	rem Set Free equal inputs ...
		if #cell "FREE" eq
			set '#cell "free"
		endif
		if #cell "Free" eq
			set '#cell "free"
		endif
	rem Set Help equal inputs ...
		if #target "?" eq
			set '#target "help"
		endif
		if #target "HELP" eq
			set '#target "help"
		endif
		if #target "Help" eq
			set '#target "help"
		endif
	rem Set List equal inputs ...
		if #target "LIST" eq
			set '#target "list"
		endif
		if #target "List" eq
			set '#target "list"
		endif
	rem Set Sub	equal inputs ...
		if #cell "SUB" eq
			set '#cell "sub"
		endif
		if #cell "Sub" eq
			set '#cell "sub"
		endif
	rem Execution Nesting	
		if #target "about" eq
			set '#mode 0
		else
			if #target "help" eq
				set '#mode 5
			else
				if #target "list" eq
					set '#mode 1
				else
					if #cell "add" eq
						if #players #target Acontains false eq
							set '#mode 7
						else
							if #targetgroup #playergroup lt not
								set '#mode 8
							else
								if #time null eq
									set '#mode 6
								else
									set '#mode 3
								endif
							endif
						endif
					else
						if #cell "free" eq
							set '#mode 4
						else
							if #cell "sub" eq
								if #players #target Acontains false eq
									set '#mode 7
								else
									if #targetgroup #playergroup lt not
										set '#mode 8
									else
										if #time null eq
											set '#mode 6
										else
											set '#mode 3
										endif
									endif
								endif
							else
								if #players #target Acontains false eq
									if #target null eq
										set '#mode 5
									else
										set '#mode 7
									endif
								else
									if #targetgroup #playergroup lt not
										set '#mode 8
									else
										if #time null eq
											set '#mode 6
										else
											if #reason null eq
												set '#mode 6
											else
												if #cell $jail_cells gt
													set '#mode 9
												else
													if #cell 1 lt
														set '#mode 9
													else
														set '#mode 2
													endif
												endif
											endif
										endif
									endif
								endif
							endif
						endif
					endif
				endif
			endif
		endif

	rem Execution Modes
		if #mode 0 eq
			say #PLAYER "§1|------------------|§r"
			say #PLAYER "§1|§f JAIL COMMAND§r"
			say #PLAYER "§1|§f     v1.0§r"
			say #PLAYER "§1|§f BY: §9XYVIUS§r"
			say #PLAYER "§1|------------------|§r"
		endif
		if #mode 1 eq
			run jailList
		endif
		if #mode 2 eq
			if #reason Asize 1 gt
				set '#reason #reason " " Ajoin
			endif
			run jailSet #target #cell #time #reason
			logprint "jailSet:" #target .. #cell .. #time .. #reason ..
		endif
		if #mode 3 eq
			run jailAdd #target #cell #time #reason
			logprint "jailAdd:" #target .. #cell .. #time ..
		endif
		if #mode 4 eq
			run jailFree #target #cell #time #reason
			logprint "jailFree:" #target ..
		endif

	rem Help Modes
		if #mode 5 eq
			rem Player Initiated Help
			set '#showhelp true
		endif
		if #mode 6 eq
			say #PLAYER "§4Invalid number of argument(s)!!!"
			set '#showhelp true
		endif
		if #mode 7 eq
			say #PLAYER "§4Your TARGET must be Logged On. §e(Check Capitalization)§r"
			set '#showhelp false
		endif
		if #mode 8 eq
			say #target "§4" #PLAYER . "§ejust attempted to use the Jail command on you.§r" ..
			say #PLAYER "§4Your TARGET must be of a lesser Rank!"
			set '#showhelp false
		endif
		if #mode 9 eq
			say #PLAYER "§4Invalid Cell ID!!! §eMust be between 1 and" $jail_cells ..
			set '#showhelp true
		endif
		if #showhelp true eq
			run jailHelp
		endif
		run jailCheck
	</event>
    <event name="jailFree">
	rem Init Vars
		set '#target '#cell '#time '#reason to #POP Aexplode
		set '#id 0
		set '#count 1
	rem Locate targets cell
		while #count $jail_cells 1 + lt
			set '#player '$jail_cell #count . "_player" .
			if #target #player evalvar eq
				set '#id #count
			endif
		inc '#count
		endwhile
	rem if found execute
		if #id 0 eq
			say #PLAYER "§4Invalid Target! §e(Check Capitalization)§r"
		else
			set '#targetgroup '$jail_cell #id . "_group" . evalvar
			execcmd #PLAYER setgroup #target #targetgroup int
			set '$jail_cell #id . "_group" . "null"
			set '$jail_cell #id . "_player" . "null"
			set '$jail_cell #id . "_reason" . "null"
			set '$jail_cell #id . "_release" . "null"
			say #PLAYER "§eRestored previous permissions for:" #target ..
			say #target "§eYou have served your time and your permissions have been"
			say #target "§erestored to their previous state. Please refrain from"
			say #target "§eany further violations of policy, as penalties will increase!"
			say #target "§ePLEASE use "§2/rules§e" to read the rules!!!"
		endif
	</event>
    <event name="jailHelp">
		say #PLAYER "§2Usage:"
		say #PLAYER "§f  /jail [LIST|HELP|?]"
		say #PLAYER "§f  /jail TARGET CELL_ID TIME REASON (Time in Minutes)"
		say #PLAYER "§f  /jail TARGET [ADD|SUB] TIME"
		say #PLAYER "§f  /jail TARGET FREE"
		say #PLAYER "§2Examples:"
		say #PLAYER "§f  (eg. /jail Xyvius 2 120 Rule 1 - 2nd Violation)"
		say #PLAYER "§f  (eg. /jail Xyvius add 30)"
		say #PLAYER "§f  (eg. /jail Xyvius free)"
	</event>
    <event name="jailList">
	rem Disp list of prisoners
		say #PLAYER "§eJail System Prisoner List"
		say #PLAYER "§2# | Cell Desc. | Prisoner | Time Remaining | Reason"
		set '#count 1
		while #count $jail_cells 1 + lt
			set '#player '$jail_cell #count . "_player" . evalvar
			set '#desc '$jail_cell #count . "_desc" . evalvar
			set '#release '$jail_cell #count . "_release" . evalvar currtime - totime
			set '#reason '$jail_cell #count . "_reason" . evalvar
			if #player null eq not
				say #PLAYER "§f" #count . "§2 | §f" . #desc . "§2 | §f" . #player . "§2 | §f" . #release . "§2 | §f" . #reason .
			endif
		inc '#count
		endwhile
		say #PLAYER "§eDone!"
	</event>
    <event name="jailMonitor">
	rem If monitor not already running Execute
		if $jail_monitor true eq
			run jailCheck
			print "Jail Monitor already running."
		else
			set '$jail_monitor true
			print "Jail Monitor Initiating..."
			set '#loop true
			while #loop true eq
				set '#players getplayers
				set '#loop false
				set '#count 1
				while #count $jail_cells 1 + lt
					set '#player '$jail_cell #count . "_player" . evalvar
					if #players #player Acontains true eq
						set '#loop true
					endif
				inc '#count
				endwhile
				if #loop true eq
					run jailCheck
					sleep 60000
				endif
			endwhile
			print "Jail Monitor Stopping..."
			set '$jail_monitor false
		endif
	</event>
    <event name="jailSet">
	rem Init Vars
		set '#target '#cell '#time '#reason to #POP Aexplode
		set '#count 1
		set '#prisoner false
	rem check if target is already prisoner
		while #count $jail_cells 1 + lt
			set '#player '$jail_cell #count . "_player" . evalvar
			if #target #player eq
				set '#prisoner true
			endif
		inc '#count
		endwhile
	rem if not execute
		if #prisoner true eq
			say #PLAYER #target "§7is already in the Jail System. Use the ADD/SUB argument to adjust their sentence." ..
		else
			set '#player '$jail_cell #cell . "_player" . evalvar
			if #player null eq
				set '#gps '$jail_cell #cell . "_gps" . evalvar
				set '#targetgroup #target getgroup
				set '$jail_cell #cell . "_group" . #targetgroup
				set '$jail_cell #cell . "_player" . #target
				set '$jail_cell #cell . "_reason" . #reason
				run jailAdd #target #cell #time #reason
				execcmd #PLAYER setgroup #target $jail_group int
				teleport #target #gps
				say #PLAYER "§e" #target . "processed and entered in to Jail System..." ..
				say #target "§eYou have been placed in jail for violating our rules."
				say #target "§eYour sentence continues to pass even if you are offline."
				say #target "§eFor more information on your sentence use §2/jailtime§e."
				sleep 2000
				say #PLAYER "§eLaunching Jail Monitor..."
				launch jailMonitor
			else
				say #PLAYER "§4Cell is already occupied by:" #player ..
				say #PLAYER "§ePlease choose an empty one. (Use §2/jail LIST§e)."
			endif
		endif
	</event>
    <event name="jailTime">
	rem Init Vars
		set '#count 1
		set '#prisoner false
	rem check which prisoner ID is players
		while #count $jail_cells 1 + lt
			set '#player '$jail_cell #count . "_player" . evalvar
			if #PLAYER #player eq
				set '#release '$jail_cell #count . "_release" . evalvar currtime - totime
				set '#reason '$jail_cell #count . "_reason" . evalvar
				set '#prisoner true
			endif
		inc '#count
		endwhile
	rem Disp Prisoner info to prisoner
		if #prisoner true eq
			say #PLAYER "§eYou have§f" #release .. "§eremaining on your sentence for your offense of:§f" .. 
			say #PLAYER "§e:§f" #reason .
			say #PLAYER "§ePlease use §2/rules§e and take this time to review them. As penalties increase with each offense."
		else
			say #PLAYER "§7You are NOT currently being held in the Jail System.§r"
		endif
	</event>
    <event name="jail_cell1_desc" value="Cell Name"/>
    <event name="jail_cell1_gps" value="500,70,500"/>
    <event name="jail_cell1_group" value="null"/>
    <event name="jail_cell1_player" value="null"/>
    <event name="jail_cell1_reason" value="null"/>
    <event name="jail_cell1_release" value="null"/>
	<event name="jail_cells" value="1"/>
	<event name="jail_group" value="1"/>
    <event name="jail_monitor" value="false"/>
```