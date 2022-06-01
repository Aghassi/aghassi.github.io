# The Journey to 2 Gig Fiber

Recently I was privileged enough to determine that AT&T now offer symmetrical 2 Gbps fiber in my neighborhood. But, as with all bleeding edge technologies, there is a catch. Most devices can't achieve this throughput speed. A lot of consumer grade wifi tops out at about 500 Mbps (a quarter of the aforementioned speeds). Most wired devices cap out at 1 Gbps. And, like with certain type of video capabilities now-a-days, all parts of a network chain have to support the throughput. This means the modem, the router, the switch, the cables, and finally the device using the internet.

So it was one thing for me to determine I was going to transition to 2 gig fiber, but it was another thing for me to plan the transition. You see, my current network stack did not support 2 gig. I was starting with multiple devices with 1 gig ethernet, an Eero 6 router (which had a 1 gig ethernet jack) and a Surfboard Modem with supported 1 gig. So nothing in my current setup supported more than a gig of throughput.

## Components

This is the most important part of the transition. Knowing what to buy. There are many options on the market that sell themselves as built for this task, but very few offer what I wanted.

- Modularity (each component could be upgraded individually)
- Aesthetically Clean Design
- User Friendly UI Console
- Advanced User Features (VPN, VLAN, Port Forwarding, etc)

After much debate, research, and asking friends, I landed on the Ubiquity stack.

- [Unifi Dream Machine Pro]()
- [Unifi 24 Port 2.5 Gig Switch]()
- [Unifi nanoHD Wifi Access Point]()
- [Monoprice Cat6 Cable]()
- [Cat6 Crimper]()
- [SFP+ to RJ45 Adapter]()
- [SFP+ to SFP+ Cable]()

I also needed to upgrade certain hard wired devices in my network so that they could take advantage of the new pipe.

- [Caldigit TS4]() - For my work laptop
- [TPLink 2.5 Gig PCIE Cards]() - For my gaming desktop and my plex server

## Installation and Setup

After the AT&T came out and did the install (which, props to them as the service person was early, communicative, and helpful), I needed to reconfigure the AT&T modem they provide to not have it conflict with the Ubiquity gear. The modem AT&T supplied me also doubled as an Optical Network Terminal (ONT), so I couldn't remove it from the chain. However, logging into the modem at it's IP address of `192.168.1.254` did allow me to set the modem to `Passthrough` mode. `Passthrough` mode allows me to pass network traffic through the modem onto a different machine which will handle routing. While I was in the modem settings, I also disabled the Wi-Fi capabilities as I won't be using them. I was lucky enough to find a [detailed Reddit thread](https://www.reddit.com/r/Ubiquiti/comments/jfb3vc/udm_pro_and_att_uverse_bgm320/) outlining how to set the modem up appropriately.

Once the modem was set, I linked it with the Dream Machine Pro by connecting a Cat6 wire from the 5 Gig port on the modem to the SFP+ port that had an RJ45 connector in it (WAN2). Then, I turned on the Dream Machine Pro and set it up for first use using the Ubiquity phone app. Everything setup fine, and I was off to the races.

### IP Subnet Range

`10.0.0.x/24`

Using `netstat` to enable the card

https://www.youtube.com/watch?v=mYYVhimigYM <-- crimp wires tutorial
Mention fancy ethernet heads

wifi speeds https://help.ui.com/hc/en-us/articles/360012947634-UniFi-Troubleshooting-Slow-Wi-Fi-Speeds-

Discuss adding APs, trade offs with mesh given my area

### Switch Fans

Default Temps

```sh
Temp (C)....................................... 46
Fan Speed, RPM................................. 5304
Fan Duty Level................................. 51%
Temperature traps range: -5 to 110 degrees (Celsius)

Temperature Sensors:
Unit     Sensor  Description       Temp (C)    State              Max_Temp (C)    Alert_Temp (C)
----     ------  ----------------  ----------  -----------------  --------------  ----------------
1        1       TA                37          Normal             41              80
1        2       PSU               37          Normal             41              80
1        3       TEMP-1            40          Normal             43              80
1        4       TEMP-2            40          Normal             44              80
1        5       MAC-1 (Local)     47          Normal             54              80
1        6       SPHY              42          Normal             47              80
1        7       SPHY              44          Normal             50              80
1        8       SPHY              46          Normal             51              80
1        9       SPHY              45          Normal             49              80
1        10      SPHY              42          Normal             48              80

Fans:
Unit Fan Description    Type      Speed         Duty level    State
---- --- -------------- --------- ------------- ------------- --------------
1    1   FAN-1          Fixed     5304          51%           Operational
1    2   FAN-2          Fixed     4931          51%           Operational

PSU and RPS status:
POE PSU Error Count............................ 0
PSU 12v PG     PSU 54v PG     RPS Present    RPS 12v PG     RPS 54v PG
------------   ------------   ------------   ------------   ------------
True           True           False          False          False
```

After FAN fix

```sh
(UBNT) >show environment

Temp (C)....................................... 35
Fan Speed, RPM................................. 3985
Fan Duty Level................................. 37%
Temperature traps range: -5 to 110 degrees (Celsius)

Temperature Sensors:
Unit     Sensor  Description       Temp (C)    State              Max_Temp (C)    Alert_Temp (C)
----     ------  ----------------  ----------  -----------------  --------------  ----------------
1        1       TA                29          Normal             29              80
1        2       PSU               28          Normal             28              80
1        3       TEMP-1            31          Normal             31              80
1        4       TEMP-2            31          Normal             31              80
1        5       MAC-1 (Local)     38          Normal             39              80
1        6       SPHY              32          Normal             33              80
1        7       SPHY              35          Normal             36              80
1        8       SPHY              35          Normal             36              80
1        9       SPHY              35          Normal             35              80
1        10      SPHY              34          Normal             35              80

Fans:
Unit Fan Description    Type      Speed         Duty level    State
---- --- -------------- --------- ------------- ------------- --------------
1    1   FAN-1          Fixed     3985          37%           Operational
1    2   FAN-2          Fixed     3970          37%           Operational

PSU and RPS status:
POE PSU Error Count............................ 0
PSU 12v PG     PSU 54v PG     RPS Present    RPS 12v PG     RPS 54v PG
------------   ------------   ------------   ------------   ------------
True           True           False          False          False
```
