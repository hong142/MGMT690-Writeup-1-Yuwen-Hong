# Threats Detection Data Pipeline
This document will introduce a general plan for building a threats detection data pipeline that works on the data from motion detectors of security systems. We will guide you through the problem that leads to the need for the pipeline, the goals we want to achieve with the pipeline, and the technologies we are going to use to build the pipeline.
## Problem
The motion detectors are deployed by many companies as well as individuals all over the world as part of their security systems to get timely alarms about intruders. Basically, a camera captures a series of images or videos, and then those images will stream into the top of certain pipeline to detect unusual motions. However, motion detectors can catch the motion of not only humans but also other non-human objects such as animals. For example, what the first picture cached is an obvious intruder, while things like the racoon in the second picture could also cause the alarm if there wasn’t any pipeline to further analyze the image data to identify the object in motion. In the first case, it's good that we can detect the motion, trigger the alarm and probably calling the police. But for the second case, if the system actually triggered the alarm because of the racoon, we will get what we called false positive detection, unless the customer do want to react to such "intruders" the same way as human intruders every time.  
<img src="https://github.com/hong142/test/blob/master/20171030195012.png" width="250" height ="250"> &nbsp; &nbsp; &nbsp; <img src="https://github.com/hong142/test/blob/master/20171030202126.png" width="250" height="250"> <br />
Picture1:Intruder_positive &nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Picture2:Racoon_potential false positive
