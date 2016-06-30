MQTT from IBM it is considered the machine-to-machine connectivity protocol. It is widely adopted, so lots of IoT solutions use it as the protocol to interconnect devices.

These series of posts will try to go through the process of creating a MQTT broker from scratch, I will try to explain the problems, solutions and considerations I met during the process.

## Topics

Let's go straight to the point, the MQTT broker is the main character of the publish/subscribe protocol. It is the responsible to keep the track of the devices that are subscribed to a topic: it filters and recieves messages from devices, and then it decides after which other devices need to be notified.

To make this possible, the MQTT broker filter the message by means of the topic, a string that contains one or more topic levels. The following example will clarify what it is exactly a topic:

'''
/house/kitchen/fridge/temperature
'''

The string use slashes to separete each level of the topic, they are case-sensitive and each level must contain at least one character.

Devices can publish and susbcribe to any level of the topic, although it is recommended to use specific topics instead of general ones.

## Trie structure




