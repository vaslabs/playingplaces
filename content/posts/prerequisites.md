---
title: "Personal remote gaming: architecture"
weight: 9
---

# Architecture

Here I'm describing what you need to have and the connectivity relationship of each component to have a complete network that allows you to:

1. Do power management over the internet
2. Have a reliable outgoing bandwidth
3. Deal with emergencies


## Ingredients

Note: I linked on the products I bought for convenience, in no way am I endorsing them.

1. 2 Smart plugs (I've used [these](https://www.amazon.co.uk/gp/product/B085LY7W7G))
2. 2 Powerline adapters (3 for testing locally although you can test with the client over the wifi, adapt if your gaming pc is in proximity with the router, mine isn't) (I've used [these](https://www.amazon.co.uk/gp/product/B01BECPIMC))
3. At least 2 ethernet cables (often included in the powerline adapters)
4. An amazon echo (theoritically you can do with any smart home assistant, it just happened I have one of those lying around)
5. A smart phone (helps with managing the smart plugs or giving commands to alexa if you are not home)
6. An internet connection with good upload bandwidth (mine is 100Mpbs, in theory you can work with much less)
7. Basic coding skills (I've used a bit of nodejs, a bit of bash and a bit of python)
8. 2 Extension cables (adapt the size according to your pc needs)
9. An amazon developer account (aws) and basic knowledge on creating Alexa skills.

## Design

As shown in the house connectiviy diagram, the router is connected to a powerline adapter and another powerline adapter is in proximity to the gaming pc, connected to it over ethernet. Be ware that in order for the powerlines adapter to work, you need to have them in the same circuit in your home.

My raspberry pi connects over wifi but a third optional power adapter can give it an additional connectivity option for redundancy. Both the RPI and the powerline adapter are connected to one extension cable which is powered on by the smart plug.

My gaming PC is connected to the other extension cable which also powers on the second powerline adapter, the PC power supply and the monitor.

The Amazon echo is connected over the wifi and powered by the main home plugs.

The raspberry pi and the gaming pc are also connected to "the cloud", to an amazon service called SQS. This is a simple messaging queue. Each machine is
connected to a different SQS. That way, they can receive commands as SQS messages and process them accordingly (more details on future section).

### House connectivity


![alt house_connectivity](/posts/images/house_connectivity.png)


### Cloud connectivity

![alt cloud_connectivity](/posts/images/cloud_connectivity.png)



### Alexa Skills

#### Smart plugs

Your smart plugs should support alexa and it's straight forward to manage them. You can also create custom routines on the alexa app to manage
the electrical power on/off.

#### Custom skill

This custom Alexa skill will help us support 4 main intents to do computer power management over the internet. 

The intents I have implemented are:

![alt custom_skill](/posts/images/remote_gaming_alexa_skills.png)

1. Turn on PC: For wake on lan to wake up the gaming pc.
2. To poweroff the PC and the raspberry pi
3. Restart steam: To gracefully or forcefully kill steam and start it again
4. Restart pc: Reboots the gaming pc



## Implementation

To learn more go [here](/posts/implementation/):
