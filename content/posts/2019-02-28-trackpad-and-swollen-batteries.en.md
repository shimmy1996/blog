+++
title = "Trackpad and Swollen Batteries"
tags = ["laptop"]
categories = ["geekery"]
draft = false
date = 2019-02-28
slug = "trackpad-and-swollen-batteries"
+++

For the last few weeks, the right click on my Dell XPS 13's trackpad is getting less responsive: the entire right half of the trackpad sunk around 2mm beneath the palm rest, making clicks hard to register. At first I dismissed it as normal wear, but it turned out that the swollen batteries lifted the left half of the trackpad to such a degree that the trackpad warped. I immediately ordered an OEM replacement (Dell JD25G) swapped out the swollen batteries. XPS 13 (9343) was a breeze to service. The screws that hold the bottom panel (a quite hefty hunk of aluminum) in place are all clearly visible and the component layout allows battery to be swapped with minimal disassembly. I also swapped out the WLAN card (Dell DW1560) for an Intel AC9560, whose drivers are in the mainline Linux kernel.

The trackpad felt normal after the battery swap, of course. However, the fact that average laptop battery starts to degrade around 18 months surprised me quite a bit. Mine lasting nearly four years is probably quite descent. Newer laptops uses prismatic cells (those slab shaped batteries also found in phones) instead of cylindrical ones, as can be found in my first laptop, Dell Vostro 3750. Roughly speaking, prismatic cells trade size for lifespan by emitting external casing and gas vents found on cylindrical cells. The battery swell is caused by gas build up, which might have been avoided in cylindrical cells with vents. It's interesting that (easily) removable batteries have largely disappeared in consumer laptops - even the large desktop replacements (to be fair, those spend most of the time plugged in anyways). The only consumer electronics that still almost always have removable batteries I can think of are cameras.

Because of the incident, I started to browse current laptops on the market as the new quad/hex core laptop CPUs are quite tempting an upgrade (my XPS 13 has a i5-5200U). I was not a huge fan of the latest XPS 13 (9380) mostly because of the port selection: I just don't have any USB Type-C devices, so the 1 Type-C plus 2 Type-A combination found on XPS 13 (9360) is superior in my opinion. Besides ports, the onboard WLAN card and removal of full-sized SD card slot also make the latest model less appealing.

I also came across the Let's Note line of laptops from Panasonic, which are reliable, lightweight business laptops that often comes with removable batteries and a wide spectrum of ports. If only they weren't so prohibitively expansive, doesn't have those ugly "Wheel Pads", and come with US keyboard layout, they are quite the ideal laptops. I like the aesthetics of 2016 CF-MX5 series the most, but that won't make much of an upgrade.

More realistic choices include HP's EliteBook, Lenovo's ThinkPad T series, and Dell's Latitude/Precision lines. I vetoed EliteBook because all of them had a huge glaring proprietary docking port that I might never use. Latitude 5491 seem to have cooling issues due to the 45W TDP CPUs, while Latitude 7390 and 7490 both seem quite descent, with options to disable Intel ME and official Linux support. ThinkPad T480 pretty much ticks everything on my list, but it seems that the next iteration T490 will no longer have the bridge battery system and only one SODIMM slot, pretty much like T480s.

Hunting for second-handed machines is also an option, but it defeats the purpose of the upgrade since my primary motivation is the new quad core CPUs. Some may argue our laptops are overpowered already, and indeed my XPS 13 still feels pretty snappy though, so I'm not in urgent need for an upgrade. However, I did come up with a list of what I want in a laptop in case the ideal candidate shows up someday.

-   Good Linux driver support.
-   Below 15 inch in size and low travel weight. XPS 13 converted me from a DTR enthusiast to an Ultrabook follower: it does feel nice to be able carry a laptop all day without feeling it.
-   Non-Nvidia graphics. Both AMD and Intel has better open source driver support and I use my desktop for tasks heavily reliant on GPU.
-   Reasonable battery life (6 hours or more) and removable battery.
-   Not-too-radical port selections, not until all mouses and flash drives default to USB Type-C at least.
-   Standard components and easy to upgrade, i.e. SODIMM slot for memory, PCIe for WLAN card/SSD.
-   A nice trackpad. I'm rather insensitive to quality of laptop keyboards so anything marginally descent would do. It would be really cool to have an ErgoDox laptop though.
