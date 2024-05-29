---
title: Proposal for a BARC packet and AMPRNet presence
author: Dan Cross <kz2x@arrl.net>
date: May 19, 2024
---

# Executive Summary

KZ2X proposes that BARC establish a packet node and digipeater,
hosted at at the club's location at Artisan's Asylum in Allston,
MA, and that it be connected to the Internet via an AMPRNet
allocation obtained from ARDC and assigned to the club.

# Background

There is a lack of AX.25-based packet-radio infrastructure in
the Boston metro area.  While there are several digipeaters
available on 145.090 MHz, they are in Brockton and Walpole and
RF availability to connect to them is highly dependent on
ambient conditions; access is often spotty.  W1XM maintains an
digipeater on 144.390 MHz, but this is exclusively for APRS and
does not support general AX.25 traffic.  There are no digital
repeaters for general packet use inside of I-95.

Despite this, NTS traffic handlers and other licensed operators
in the greater Boston area use AX.25-based packet BBSes for
radiogram traffic and for general use.  The lack of packet
infrastructure in the metropolitan area makes this challenging.

Moreover, several amateurs in the area are experimenting with
non-NTS and non-APRS use of AX.25; lack of a local digipeater
creates challenges with collaboration and access.

Somewhat related, there exists an Internet network dedicated to
amateur radio, administered by the Amateur Radio Development
Corporation (ARDC), who's grant was used to sponsor the club's
space at Artisan's Asylum (AA) in Allston.  This network is
already used by several amateurs in the area to support
radio-related activities.  For more information, see
https://wiki.ampr.org.

# Proposal

With the establishment of the club's presence at AA, there is an
opportunity to establish a packet node running on an AMPRNet
subnetwork, supporting AX.25 packet and other data-related radio
activities in the greater Boston area.

This document is a detailed proposal suggesting that the club
establish an AMPRNet subnetwork at AA, and establish a packet
station on that subnetwork, providing traffic and other
services in the metro area.

# Services provided

The ultimate goal is to provide the following services to Boston
amateur radio community:

* An AX.25 packet digipeater/node (under the W1BOS call sign)
* An AX.25 packet BBS
* A node on the global Convers network
* Validating, Caching, Recursive DNS resolution to the east
  coast AMPRNet community
* A GPS disciplined Precision Time Protocol "Grandmaster" clock
  and stratum-1 Network Time Protocol service to the east coast
  AMPRNet community
* Support for internal club operations

## Packet node

The packet node will provide AX.25 services on 145.090 MHz at
1200 BAUD (AFSK, no FEC) under the `W1BOS-1` callsign and SSID.
In princple, this could be expanded over time if additional
radios/TNCs are attached to the station.

The packet node will also be accessible via `telnet` from other
AMPRNet nodes.

## DNS

We will provide a validating, recursive, caching DNS resolver on
the AMPRNet subnetwork.  This will be useable for BARC computers
at the AA site, as also accessible from other AMPRNet hosts.
The intent is to be a reliable DNS source for the northeast
AMPRNet community.

## Time Service

A GPS-disciplined, Precision Time Protocol (PTP) "Grandmaster"
clock server will be installed on the AMPRNet subnetwork.  This
will provide nanosecond-resolution time to computers at the AA
site, and used as a time source for a stratum-1 NTP server,
which will be accessible to other AMPRNet hosts.  The intent is
to be an accurate, reliable, stable time source for the
northeast AMPRNet community.

# Resource Requirements

The resources required to implement this proposal are modest,
and will mostly be donations.  At a high level, the following is
required:

* Sufficient computer and networking hardware to host desired
  services detailed above
* Power infrastructure sufficient to run the computers,
  networking, and radio equipment
* A router/firewall suitable for connection to the AMPRNet mesh
  network, and/or possibly participating in BGP routing
* A single, stable, externally accessible IP address provided by
  AA IT for routing the AMPRNet allocation to the equipment in
  the club's space
* An Ethernet switch for interconnecting equipment inside the
  AMPRNet allocation
* A transmission line cable to connect a suitable radio to an
  exterior antenna at the AA site
* An exterior antenna for the packet station
* A monoband 2m VHF FM radio for the packet station
* A hardware TNC for the packet station
* Miscellaneous cables for USB, Ethernet, power, etc.

## Power

Sufficient electrical power to run the radio, computer, and
networking hardware is required.  Power for the computer and
networking equipment will come from the local electrical
utility.  A single, standard, 120VAC/15A electrical outlet will
connect an uninterruptible power supply, which will provide
power conditioning and battery backup to the computer and
network devices.

The radio will be powered via the existing shack power supply,
via an existing DC distribution box or direct connection.

A block diagram of the required power tree follows:

```
              .─────────────────────.
             (     Utility power     )
              `──────────┬─────┬────'
                         │     │
                      120VAC   └─────────────120VAC───────────────┐
                         │                                        │
              ┌──────────▼──────────┐                     ┌───────▼──────┐
              │                     │                     │Shack DC Power│
     ┌────────┤   APC BackUps 425   ├──────────┐          │    Supply    │
     │        │                     │          │          │  (Existing)  │
     │        └──┬────────────┬─────┘          │          └───────┬──────┘
     │           │            │                │                  │
  120VAC      120VAC       120VAC           120VAC                │
 (Battery)   (Battery)    (Battery)        (Battery)              │
     │           │            │                │                  │
     │           │            │                │                  │
┌────▼───┐  ┌────▼───┐  ┌─────▼──────┐  ┌──────▼──────┐    ┌──────▼──────┐
│ RPi PS │  │ RPi PS │  │ MEAN WELL  │  │  Ubiquiti   │    │DC Power Dist│
└────┬───┘  └────┬───┘  │ LRS-150-12 │  │EdgeRouter 4 │    │(RigRunner?) │
     │           │      └────┬─────┬─┘  └─────────────┘    └──────┬──────┘
     │           │           │     │                              │
  5VDC/3A     5VDC/3A    12VDC/3A  └──12VDC/2A─┐               13.8VDC
     │           │           │                 │                  │
     │           │           │                 │                  │
┌────▼───┐  ┌────▼───┐  ┌────▼───┐      ┌──────▼──────┐    ┌──────▼──────┐
│ RPi 5  │  │ RPi 5  │  │RPi CM4 │      │     FS      │    │    Yaesu    │
│(Packet)│  │(Server)│  │ (Time) │      │IES3110-8TF-R│    │ FTM-3100R/E │
└────┬───┘  └────────┘  └────────┘      └─────────────┘    └─────────────┘
     │
 5VDC/500mA
   (USB)
     │
  ┌──▼──┐
  │ TNC │
  └─────┘
```

Note that the MEAN WELL power supply technically single output,
it has lugs for two connections and is capable for delivering
12.5A continuous at 12V, well beyond the current draw expected
of the two devices it powers.

## Hardware

The following hardware will be used used:

* A router/firewall to the connect the club's AMPRNet allocation
  (Ubiquiti Edgerouter 4)
* A suitable computer to host internal network services (DHCP, DNS*)
  (Raspberry Pi 5 "single board computer")
  [*] Note that DNS is also externally facing
  networking services.
* A computer suitable for hosting stratum-1 time PTP service
  (Raspberry Pi Compute Module 4 with GPS hat and IO board)
* A suitable computer to host the packet node
  (Raspberry Pi 5 "single board computer")
* A network switch for interconnecting these resources
  (FS IES3110-8TF-R)
* A VHF monoband FM radio for packet operations
  (Yaesu FTM-3100H)
* A hardware TNC for packet
  (TARPN NinoTNC SMT)
* A power distribution system for all of the above
* Various cables (Ethernet, USB, serial, etc)
* GPS and 2m antennas

## Artisan's Asylum

Artisan's Asylum will be the primary site of the club's AMPRNet
and packet presence.  For this project, the following resources
are required from AA:

* A single Internet-visible IP address allocated to the club's
  router for connecting to the larger AMPRNet mesh network
  - Bonus points if we can get AA to allow us to use BGP routing
    for AMPRNet.
* Power infrastructure sufficient for:
  - 3x Raspberry Pi class single board computers
  - An Ethernet switch
  - A router appliance (Ubiqiti Edgerouter 4)
  - A TNC (powered by USB from a Raspberry Pi)
  - A VHF radio
* antenna access
  - Access to run a transmission line to a roof-mounted VHF
    antenna for the packet station
  - Exterior access to set up an antenna for the packet station
  - Current expectations are that the active GPS antenna will
    work well enough indoors, without requiring external access

## ARDC

ARDC administers the global AMPRNet.  The club will coordinate
with them for the allocation of a subnetwork, to be hosted at
the AA site.  This subnetwork _should_ be allocated from the
44.44.0.0/16 network dedicated to the state of Massachusetts;
ideally the assigned network would be logically "near" those
allocated to stations in Middlesex County.

The club's allocated subnetwork will terminate at our router.
Hopefully, we can route directly by advertising a route to our
subnet via the Border Gateway Protocol (BGP); in all cases, we
will have support IPENCAP tunnels to other AMPRNet subnetworks.

# Support

A critical design goal in this proposal is supportability on an
ongoing basis.  To that end, the configuration is designed to be
robust and standard.

KZ2X will do the initial setup and documentation, and share this
widely with the club.

## Software Configurations

* The router runs the OpenBSD operating system and 44ripd
* Each Raspberry Pi runs the Raspberry Pi distribution of Linux
* Console access to all hardware is via serial ports
* Administrative credentials will be shared with club officials,
  kept in seal envelopes until needed.
* Copies of configuration files, and so forth, will be kept in a
  `git` repository using the Club's github organization

# Opportunities for future expansion

## Additional RF packet interfaces

The proposed infrastructure will be initially provisioned with a
single RF packet node on 145.090 MHz, but there's no structural
reason the infrastructure couldn't support multiple nodes on
different bands.  Each additional interface would require access
to a suitable radio, antenna, transmission line, and TNC, though
it may be possible to multiplex antennas based on bands.

Candidates for packet expansion include supporting 1.25m and
70cm.  These could operate at higher speeds, or use different
link-layer protocols (e.g., the "Improved Layer 2 Protocol",
IL2P, which supports forward error correction and more
efficient data representation than traditional AX.25).

The Raspberry Pi 5 has 4 USB interfaces to connect to TNCs, but
this could be expanded by means of a dedicated USB hub should
more devices be desired.

## APRS Digipeater

Similarly, the infrastructure could support APRS on 144.390 MHz,
providing a digipeater similar to MIT's.

## Additional AMPRNet services

The existing hardware could support additional AMPRNet services,
as well.  A web server could be installed, for instance, and
configured to interface with the packet node.  The club could
configure IP over AX.25, for example, or set up a VPN to the
to access the W1BOS DMR repeater.

Similarly, the club could support AX.25 over IP.

# Appendix 1: Graphical Representation of Network

```
                               .───────────────.
                          _.──'     Internet    `───.
                         ╱                           ╲
                        (       (Connection from      )
                         `.   Artisan's Asylum IT)  ,'
                           `───.               _.──'
                                `──────▲──────'
                                       │
                                       │
                                       │
                            ┌──────────┴────────────┐
                            │   Router / Firewall   │
                            │                       │
                            │(Ubiquiti Edgerouter 4)│
                            └──────────┬────────────┘
                                       │  44.44.??.1
                                       │
                                       │
┌─────────────────┐             .──────▼──────.  44.44.??.2
│                 │         _.─' W1BOS AMPRNet `──.
│ Existing Shack  │        ╱         Subnet        ╲
│    Computer     ◀───────(      44.44.??.0/24      )
│   44.44.??.14   │        `.                     ,'
│  (PC, Windows)  │         ╱`──. (FS Switch) _.─'╲
│                 │        ╱     `─────┬─────'     ╲
└─────────────────┘       ╱            │            ╲
                         ╱             │             ╲
                    +───+              │              +───+
                   ╱                   │                   ╲
                  ╱                    │                    ╲
        ┌────────▼────────┐   ┌────────▼────────┐   ┌────────▼────────┐
        │                 │   │  Time Server:   │   │  Packet Node:   │
        │  Local Server:  │   │ GPS Disciplined │   │  LinBPQ, BBS,   │
        │    DHCP, DNS    │   │PTP Grandmaster, │   │  Convers, etc   │
        │   44.44.??.3    │   │  NTP Stratum-1  │   │   44.44.??.5    │
        │ (Raspbery Pi 5) │   │   44.44.??.4    │   │(Raspberry Pi 5) │
        │                 │   │(Raspbery Pi CM4)│   │                 │
        └─────────────────┘   └─────────────────┘   └────────▲────────┘
                                                             │
                                                            USB
                                                             │
                                                     ┌───────▼───────┐
                                           +──┬──+   │ Hardware TNC: │
                                            ╲ │ ╱    │(TAPRN NinoTNC)│
                                             ╲│╱     └───────▲───────┘
                                              │              │
                                              │       ┌──────▼──────┐
                                              └───────┤   2m VHF    │
                                                      │ Monobander  │
                                                      └─────────────┘
```

# Appendix 2: Detailed Schedule of Materials

The following materials are anticipated at the AA site:

| Item               | QTY | Desription                          | Source | Disposition | Status
| :----------------- | :-- | :---------------------------------- | :----- | :---------- | :-----
| Router             | 1   | Ubiquiti Edgerouter 4               | KZ2X   | Donated     | On-hand
| Ethernet switch    | 1   | FS IES3110-8TF-R                    | KZ2X   | Donated     | On-hand
| SBC                | 2   | Raspberry Pi 5                      | KZ2X   | Donated     | On-hand
| SBC case           | 2   | 3D printed case for RPi 5           | KZ2X   | Donated     | On-hand
| Compute module     | 1   | RPi Compute Module 4 (8G/32)        | KZ2X   | Donated     | On-hand
| CM4 IO board       | 1   | IO carrier board for CM4            | KZ2X   | Donated     | On-hand
| CM4 case           | 1   | Waveshare CM4 IO board case         | KZ2X   | Donated     | On-hand
| GNSS module        | 1   | Timebeat GNSS/GPS CM4 module        | KZ2X   | Donated     | On-hand
| GNSS module riser  | 1   | 2.5mm riser for CM4 GNSS Module     | KZ2X   | Donated     | On-hand
| SSDs               | 3   | Samsung 980 M.2 NVMe SSDs           | KZ2X   | Donated     | On-hand
| NVMe interface     | 2   | Pimoroni NVMe base for RPi5         | KZ2X   | Donated     | On-hand
| PCIe NVMe riser    | 1   | PCIe M.2 riser for CM4 NVMe         | KZ2X   | Donated     | On-hand
| 5V power supply    | 2   | 5.1V, 3A power adapter (RPi)        | KZ2X   | Donated     | On-hand
| 12V power supply   | 1   | MEANWELL LRS-150-12 (SW, CM4)       | KZ2X   | Donated     | On-hand
| Safety flange      | 1   | 3D printed flange for MEANWELL      | KZ2X   | Donated     | On-hand
| AC power cable     | 1   | Power cable for LRS-150-12          | KZ2X   | Donated     | On-hand
| DC power cable     | 1   | 18ga wire to wire (Ethernet switch) | KZ2X   | Donated     | On-hand
| DC power cable     | 1   | 18ga 2.1mm plug to wire (CM4)       | KZ2X   | Donated     | On-hand
| Ethernet cable     | 1   | Patch cable firewall to AA netowrk  | W1BOS  | Owned       | Needed
| Ethernet cables    | 3   | Cable Matters Cat 6a (6 feet)       | KZ2X   | Donated     | On-hand
| SFP+ eth cable     | 1   | SFP+ patch (switch to router)       | KZ2X   | Donated     | On-hand
| USB cable          | 1   | USB 2.0 A to B (RPi to TNC)         | KZ2X   | Donated     | On-hand
| Console cable      | 1   | RJ45 to DB9 serial ribbon for SW/FW | KZ2X   | Donated     | On-hand
| RPi debug module   | 1   | Debug module/cables for consoles    | KZ2X   | Donated     | On-hand
| USB serial adapter | 1   | 3V3 USB TTL UART for CM4 cons       | KZ2X   | Donated     | On-hand
| USB serial adapter | 1   | RS-232 DB9 UART for console         | KZ2X   | Donated     | On-hand
| USB hub            | 1   | 4 port USB hub for consoles         | KZ2X   | Donated     | On-hand
| USB A to C adapter | 1   | USB C to A adapter for laptop       | KZ2X   | Donated     | On-hand
| TNC                | 1   | TARPN NinoTNC SMT                   | KZ2X   | Donated     | On-hand
| TNC Case           | 1   | 3D printed case for NinTNC SMT      | KZ2X   | Donated     | On-hand
| 6 ft DIN6 cable    | 1   | 6 ft mini-DIN-6 cable for TNC       | KZ2X   | Donated     | On-hand
| Mic cable          | 1   | 2 ft 6P6C flat ribbon cable         | KZ2X   | Donated     | On-hand
| 3.5mm mono cable   | 1   | 3 ft 3.5mm mono cable               | KZ2X   | Donated     | On-hand
| TNC adapter board  | 1   | TNC interface adapter board         | KZ2X   | Donated     | On-hand
| TNC adpt case      | 1   | 3D printed case for TNC adapter     | KZ2X   | Donated     | On-hand
| VHF FM radio       | 1   | Yaesu FTM-3100H 2m monoband         | KZ2X   | Donated     | On-hand
| 2m antenna         | 1   | 5/8 λ vertical (homebrew)           | W1BOS  | Acquired    | Needed
| Feedline           | 1   | LMR400/equiv transmission line      | W1BOS  | Acquired    | Needed
| Power cable        | 1   | Power for FTM-3100H                 | KZ2X   | Donated     | Needed
| UPS                | 1   | APC Back-UPS 425 UPS                | KZ2X   | Donated     | On-hand
| DC power junction  | 1   | DC Power distribution junction      | W1BOS  | Owned       | Existing
| Mounting hardware  | 1   | Antenna mounting bracket etc        | W1BOS  | Owned       | Needed
