---
title: "Implementing remote gaming"
weight: 10
---

# Implementation

## Enable wake on lan

In order to turn on the PC without touching it, we need to send a magic packet over ethernet. There are a lot of tools that you can use to test
if wake on lan works (android apps, the command line ethtool) but the first step is to find the setting on your BIOS.

There's no single answer where this setting will be, mine was on advance and ACS management.

Once you verified this works you can test it to make sure that you can at least wake up your PC on your lan.



## Building the custom skill

In order to turn on the PC now over the internet we need the RPI to send a WoL message to the gaming PC (my machine is behind NAT but even if it wasn't I wouldn't expose it to a WoL over the internet).

To create an alexa skill, you need a web service to serve the alexa requests. You can simply create a lambda function. I have a very simple one:

```javascript
const AWS = require('aws-sdk');

const sqs = new AWS.SQS({apiVersion: '2012-11-05'}); 

const RPI_QUEUE = "https://sqs.eu-west-1.amazonaws.com/<home_actions>";
const GAMING_PC_QUEUE = "https://sqs.eu-west-1.amazonaws.com/<gaming-pc-actions>";

exports.handler = (event, context, callback) => {
 

  sqs.sendMessage(params(RPI_QUEUE, event), function(err, data) {
    if (err) {
      console.log("Error", err);
      context.succeed(response);
    } else {
      console.log("Success", data.MessageId);
      context.succeed(response);
    }
  });
  
  sqs.sendMessage(params(GAMING_PC_QUEUE, event), function(err, data) {
    if (err) {
      console.log("Error", err);
      context.succeed(response);
    } else {
      console.log("Success", data.MessageId);
      context.succeed(response);
    }
  });
};


function params(queueUrl, event) {
    const params = {
      DelaySeconds: 10,
      MessageAttributes: {
      },
      MessageBody: JSON.stringify(event.request.intent),
      QueueUrl: queueUrl
    };
  
  return params;
}


const response = 
{
  "version": "1.0",
  "sessionAttributes": {
  },
  "response": {
    "outputSpeech": {
      "type": "PlainText",
      "text": "Roger that"
    },
    "shouldEndSession": true
  }
}

```

Now you need to create 2 sqs queues and 2 iam users. I'm not covering this but in summary:
1. Each iam user will have read access to 1 queue.
2. Using aws configure on both your gaming pc and rpi setup the aws credentials.


This lambda function sends the skill intent to both queues and it's up to our implementation how we process them in each machine.

[This is my code](https://github.com/vaslabs/home-automation) that I run on both the RPI and the gaming pc


## Turning on Raspberry pi over the internet

We need this as a dependency of sending a WoL message to the gaming PC. Luckily RPIs boot along with turning on the plug. So, if you connect your RPI to the smart plug, you can turn it on using Alexa or the phone app of the smart plug.


## Game controlles without steam input (teasing version)

Assuming that the gaming pc is behind NAT but the gamepad host is not:

ps4 gamepads work with:
1. Start ssh server on gamepad hosting machine
2. Start ds4drv on both machines to emulate xbox
```
ds4drv --emulate-xpad-wireless --next-controller --hidraw
```
Find the xbox controller on both devices
e.g.
```python
import evdev
evdev.InputDevice("/dev/input/event27").name
```

On the steam running machine forward all output of the gamepad hosting machine to the xbox file path
 ```bash
 ssh username@gamepadhost cat /dev/input/event20 >/dev/input/event26
 ```

If both are behind NAT the best course of action would be to create a netcat proxy that both machine can connect to (if there's interest I'll cover this in the future)

## Summary

We are almost there, if you have this working you can now play most games and manage your gaming PC over the internet effectively.

Things that are missing and you might be interested at.

1. Remote gamepad setup to play games that don't work with steam input.
2. More detailed explanations on any of the components I described.
3. What to do if the Router crashes

I will cover more things in the future but it would help if you told me your opinion on what you want covered [here](https://www.reddit.com/r/linux_gaming/comments/nksdry/personal_stadia/)