---
title: "Results"
weight: 6
---

My experience with remote gaming varies per game and the internet connection I have in each location. The games I play are not so sensitive
to lag. In fact the most sensitive game I play is PES 2021 but I have no problems following the gameplay.

The best experience is behind a 40-80Mbps connection (with steam link's network test). An ethernet connection is needed though for a smooth connection.

At 10-20Mbps I have to reduce the resolution at around 1600x900 have a smooth experience.

![steam_link_resolution](/posts/images/resolution_settings.png)


Further tweaks with frame rates can probably enhance the experience on lower connections even more (probably in agreement with the game).


## Per game results

I have played recently the following games without issues (without the need for custom gamepad streaming):

1. Overcooked 2
2. eFootball PES 2021 SEASON UPDATE
3. Can't Drive This
4. The Outer worlds
5. Cyberpunk 2077

The games above except from outer worlds also play with the [custom gamepad streaming](/posts/gamepads). Also I can play the games below only with the xpad support and thus remotely with the custom gamepad solution.

1. Assassin's Creed Odyssey
2. Overcooked All you can eat
3. Worms W.M.D
4. It takes two
5. Mass Effect Andromeda
6. Unravel Two


Without the custom gamepad streaming I would lose access to a lot of my games while being away, so for my case it has been a valueable solution


## Handling failures

Building the multiple failsafe mechanisms on managing the PC remotely has been "life saving". Smart plugs helped with
PC freezes and having the Alexa skill initiate "reboot system", "reboot steam", "start steam" has also been useful to recover faster.

The most worrying failure so far was that after a forced reboot steam got in a corrupted state. Every time I was trying to start it
(remotely) it errored with a libc.so.6 not found error. Luckily a few days back I had setup TeamViewer as an alternative method of gaining
access to my PC which allowed me to reset steam.

Having multiple partitions for the games was also helpful as I lost only the games that were on the main .steam/ location.