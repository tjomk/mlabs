---
title: "Modelling Rc Car Part 1"
date: 2020-07-26T22:30:12+03:00
draft: true
---
We start with the basic components in Simulink:

1. Battery: Powertrain Blockset -> Energy Storage and Auxilary Drive -> Datasheet Battery (https://www.mathworks.com/help/autoblks/ref/datasheetbattery.html)
2. Hall effect sensors - to measure the motor RPM so we can control the speed and use it in PID
