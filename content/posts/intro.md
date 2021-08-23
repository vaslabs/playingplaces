---
title: "Personal remote gaming platform"
weight: 1
---

# Introduction

For a while now, I've been playing games on linux. There are various tutorials on how to get started using Lutris, steam, or just wine/proton. In this ad-hoc blog series, I'll describe how I've setup my own personal remote gaming with the ability to play games from anywhere in the world, using steam and only Linux OS.

More specifically my setup as of completing my "personal stadia" is powered by a Gaming PC featuring a 5700 rx AMD graphics card with the main client being a high end workstation laptop. Both are running Fedora 34 (no dual boot, no anything else). I also have a Pixel 3 running stock Android and a Raspberry Pi 4 running raspbian.

Given these, an amazon echo and some IoT peripherals, I'll describe how you can build your own personal stadia. On one hand we are going to rely heavily on steam but we are going to add a few missing features that will allow us to reliably play  anywhere in the world any game that can be played on Linux.

## Motivation
So, why did I do this? As someone pointed on [reddit](https://www.reddit.com/r/linux_gaming/comments/nksdry/personal_stadia/gzf3wrq/):
> Steam Link app can send WoL pings to registered PCs. All you need to do is to enable WoL in Mobo settings, enable auto login and set Steam to start automatically.

This is largely true, if you have reached this point, there are few extra toppings in completing your personal remote gaming platform.

Also this article is meant to inspire and I hope I won't go into deep implementation details, but sometimes I get carried away.

### Power Management
Your machine can freeze while playing. You don't want your gaming PC to remain locked, sometimes at 100% cpu usage risking high temperatures until you return home. You need a way to reset it from anywhere in the world.

Steam can crash; you cannot connect to your machine via steam link anymore. Unless you are not behind NAT and you can ssh to it, you need an alternative method to start steam again.

Steam can freeze: Although it is open, steam link doesn't get a response from it and connection is hunged. You need a way to restart steam and since your machine is responsive, it is a shame to reset it or force a power off.

### Origin/Uplay games
Steam input is problematic for most origin and uplay games, because they have their own overlay. One way that you can play with game controllers is for the games to recognise them while steam input is disabled.

This is simpler to do when not connected via steam link. If you are using ps4 controllers, you can use ds4drv and emulate an xbox controller. If you are remote though, this is a bit more complicated as (at least as of today) I haven't found a way to turn ps4 off on steam link, resulting in duplicate controllers showing up.

So we need to find a solution (if we want to play any working game) for the gamepads as well.

### Router crashes

If your home router crashes, there's no way now to turn off your PC. Although, I have not completed the work on this, I have 2 solutions in progress that will be available soon.

One is to make each machine healthcheck itself. If it loses internet access for a certain amount of time (e.g. 5 minutes), then it powers off itself.

The other way is a robotic arm that I've assembled, uses arduino as the brain. Connect a gsm shield to it and you can manually send an sms to press the button and force the computer to shut down. I will give more information on these when I complete them.


## Design
Read [here](/posts/prerequisites) to get the big picture (not the steam one) on how things are connected and the components I've used.