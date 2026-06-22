---
title: Silencing my Nutanix NX-8235 Server
date: 2026-06-22
tags:
  - embedded
  - c
  - stm32
  - uart
description: Silencing my Nutanix NX-8235 Server
---
# Overview
I own a Nutanix NX-8235 server, and it's very loud and very loud is an understatement, especially if it is in your bedroom 😅, so I tried different methods to silence it, and I will put them down below. They might help you, even though half of my attempts have not worked sadly, but they might for you.

Please pay attention to Attempt No 3 because even if you were able to minimize the noise coming from the chassis fan, you have to silence the elephant in the room, the [PSU](https://en.wikipedia.org/wiki/Power_supply_unit_(computer)) fans. For my case, the [PWS-2K22A-1R](https://store.supermicro.com/us_en/2200-1u-pws-2k22a-1r.html)
# Attempt No - 1
My First attempt at silencing that monstrosity was trying to control the fans using remote management. I dug inside the IPMI. The only thing I found related to fan speed was FAN Mode 
![IPMI Page](/projects/Attachments/172_16_161_53.png)
Setting the FAN Mode to PUE2 drops the chassis fan speeds significantly, even though they randomly spin and make your ears bleed, and the thermal thresholds are garbage when the CPU hit 50C, the [BMC](https://www.supermicro.com/en/glossary/baseboard-management-controller) assumes that the world is gonna end.

# Attempt No - 2
Using IPMI tools to control fan speeds as it can be seen multiple people have achieved good result using the method found at ServeTheHome forms [Here](https://forums.servethehome.com/index.php?threads/supermicro-x9-x10-x11-fan-speed-control.10059/page-10), this method is basically sending raw commands to the [BMC](https://www.supermicro.com/en/glossary/baseboard-management-controller) using [ipmitool](https://github.com/ipmitool/ipmitool)/[ipmicfg](https://www.supermicro.com/en/solutions/management-software/ipmi-utilities), for this example i will be using the opensource ipmitool.

using this command it should technically set the [PWM](https://en.wikipedia.org/wiki/Pulse-width_modulation) [duty cycle](https://en.wikipedia.org/wiki/Duty_cycle) for the zone you choose as it can be seen in the command and table values are in [hexadecimal](https://en.wikipedia.org/wiki/Hexadecimal)

```bash
ipmitool raw 0x30 0x91 0x5A 0x3 0x10 0x7F
```

| Byte | Value  | Meaning                                   |
| ---- | ------ | ----------------------------------------- |
| 1    | `0x30` | Supermicro OEM NetFn                      |
| 2    | `0x91` | Fan control command                       |
| 3    | `0x5A` | OEM magic value / fan controller selector |
| 4    | `0x03` | Set PWM duty operation                    |
| 5    | `0x10` | Zone 0 (CPU/System)                       |
| 6    | `0x7F` | PWM duty value (~50%)                     |
Make sure you set your **Lower Threshold Terms**! Read the post made by [DaveInTexas](https://forums.servethehome.com/index.php?members/daveintexas.38567/) for more information about the topic.

If this method works for you i recommend you use [smfc](https://github.com/petersulyok/smfc) instead of making your own script. It is a very well-maintained project and should work for most Supermicro X10-X13 motherboards other than mine, of course 🥲.

# Attempt No - 3
Using External controller, I'm currently working on releasing it on my GitHub page, and at the time of release, I will update this attempt because it is a very long journey 😅 here are some photos while replacing the [PSU](https://en.wikipedia.org/wiki/Power_supply_unit_(computer)) fans on the [PWS-2K22A-1R](https://store.supermicro.com/us_en/2200-1u-pws-2k22a-1r.html)

![Fan Driver Circuit](/projects/Attachments/20260621_005354.jpg)

![Fans](/projects/Attachments/20260621_005331.jpg)

![PSU Fan connections](/projects/Attachments/20260621_005325.jpg)
