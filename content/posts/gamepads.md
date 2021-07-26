---
title: "Personalised gamepad streaming"
weight: 
---


## Motivation

In my own experience, hot plugging gamepads makes steam a bit unstable on Linux. It can freeze or the steam input may crash.
This is particularly noticeable with remote play. The reason is that every time you connect and disconnect, the virtual controllers
are being destroyed and recreated. This may result in unresponsive games or steam to freeze.

Some games are also problematic with steam input and, again in my own experience, with dual shock 4 controllers. For example, Assassin's Creed Odyssey doesn't recognise the controllers with steam input ps4 support (something that changed recently). When I emulate them as xpad controllers though the game is able to work with them (by disabling steam input or disabling ps4 support and enabling xbox support).

![disable_ps4_enable_xbox](/posts/images/allow_xbox_support.png)

In order to have such games work and avoid instability when remote playing, I decided to create my own infrastructure to support gamepad streaming.


## Solution

### My setup

My gaming PC sits in my apartment behind NAT. So connecting to it directly is not an option. Also, when I'm travelling, there's no guarantee my portable machine will be reachable from the internet. 

In other words, I need a middleware that's accessible over the internet to help my machines communicate with each other.

My laptop and gaming pc are running Fedora 34 and I have a high end Android phone which can stream with steam link reliably.

My solution focuses on communication between my laptop and gaming pc, while I'm not really bothered to stream from my phone.

### Design

The diagram below demonstrates the basic idea of my solution.


![connect_controllers](/posts/images/connect_controllers_diagram.jpg)

With this, we basically have permanent controllers connected to the gaming pc which steam sees all the time, so there's no hot plugging going on.
We forward their input from the laptop to the middleware where the gaming pc can pull them. The communication can happen securely over ssh.


### Implementation

I created an ec2 instance (t3.micro) on aws which is on free tier for 1 year. On the eu regions it costs roughly £10 per month if you are running it constantly. 

The ec2 instance runs ubuntu and I've installed ds4drv on it and started it (haven't automated that part yet).

Initially it was creating devices on event3 and event4 but that changed to event4 and event5. I don't tear it down frequently so I pretty much have these hardcoded for now but the scripts can be improved to detect these automatically (with a similar way as done [here](https://github.com/vaslabs/home-automation/blob/f49e21d867fb4efee433aaff80ee392cd65cefc3/find_game_proxy.sh#L13) and [here](https://github.com/vaslabs/home-automation/blob/f49e21d867fb4efee433aaff80ee392cd65cefc3/find_game_proxy.sh#L18) but over ssh)


Now, with the infrastructure ready, we need a convenient way to initiate the connection. You could do it by enabling steam input on remote play
and start a terminal and write the commands but that would be very time consuming . To help me with that, I have an Alexa skill that is configured to understand an intent for connecting gamepads. This essentially, starts ds4drv on the gaming pc, connects via ssh to the middleware and directs the output of /dev/input/eventx (on the middleware) to the /dev/input/eventy (on the gaming pc).

I have a bunch of aliases to initiate the connection on my laptop (since it's the one I have direct physical access to I don't need anything more than that).

These aliases and functions help me speed up things:
```bash
alias ps4_as_xbox='ds4drv --emulate-xpad-wireless --next-controller --emulate-xpad-wireless --hidraw'
alias ps4='ds4drv --next-controller --hidraw'

alias ps4_as_xbox_four_players='ds4drv --emulate-xpad-wireless --next-controller --emulate-xpad-wireless --next-controller --emulate-xpad-wireless --next-controller --emulate-xpad-wireless --hidraw'
function connect_first_controller {
  cat /dev/input/event$1 | ssh ubuntu@$CONTROLLER_HOST sudo tee -a /dev/input/event4 >/dev/null
}

function connect_second_controller {
  cat /dev/input/event$1 | ssh ubuntu@$CONTROLLER_HOST sudo tee -a /dev/input/event5 >/dev/null
}

function connect_third_controller {
  cat /dev/input/event$1 | ssh ubuntu@$CONTROLLER_HOST sudo tee -a /dev/input/event6 >/dev/null
}

function connect_fourth_controller {
  cat /dev/input/event$1 | ssh ubuntu@$CONTROLLER_HOST sudo tee -a /dev/input/event7 >/dev/null
}
```

You can probably notice that I have support for up to 4 controllers. Again, nothing really smart coding wise to reduce duplication
because most of my games don't support more than 4 local coop players. There are also other limitations that make more than 4 inconvenient (
    like not having more than 3 friends or because the bluetooth on my laptops motherboard stops behaving nicely after a few controllers are connected.
)


To get a more idea on streaming controllers (and moving up to 4) [these are my changes to support 4 controllers](https://github.com/vaslabs/home-automation/pull/1)


### Streaming the controllers

The steps for a reliable connection with the setup above are:

1. Start [ds4drv](https://github.com/chrippa/ds4drv) on the machine you have physical access to (let's call this, the laptop), emulating xpad and on hidraw mode.

![ds4drv_emulate_xpad](/posts/images/ds4drv_emulate_xpad.png)

2. Connect the controllers normally on the laptop
3. Disable device input on the steam link running on the laptop.
![disable_steam_link_input](/posts/images/disable_steam_link_input.jpg)
4. Start remote play
5. Send the command for the gaming pc to connect to the middleware (the steps for this are [here](https://github.com/vaslabs/home-automation/blob/f49e21d867fb4efee433aaff80ee392cd65cefc3/stream_controllers.sh))

Then wait for steam to show that the xbox controllers are connected.

This was my test to demonstrate it working

{{< rawhtml >}}    
<iframe width="560" height="315" src="https://www.youtube.com/embed/lTGJEHK0MZQ" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
{{< /rawhtml >}}
