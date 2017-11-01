# Threats Detection Data Pipeline Project
This document will introduce a general plan for building a threats detection data pipeline that works on the data from motion detectors of security systems. We will guide you through the problem that leads to the need for the pipeline, the goals we want to achieve with the pipeline, and the technologies we are going to use to build the pipeline.
## Problem
The motion detectors are used by many companies as well as individuals all over the world as part of their security systems to get timely alarms about intruders. Basically, a camera captures a series of images or videos, and then those images will stream into the top of certain pipeline to detect unusual motions. However, motion detectors can catch the motion of not only humans but also other non-human objects such as animals. For example, what the first picture catched is an obvious intruder, while things like the raccoon in the second picture could also cause the alarm if there wasn’t any pipeline to further analyze the image data to identify the object in motion. In the first case, it's good that we can detect the motion, trigger the alarm and probably call the police. But for the second case, if the system actually triggered the alarm because of the raccoon, we will get what we called **false positive detection**, unless the customer do want us to react to such "intruders" the same way as human intruders every time.  <br />
<br />
<img src="https://github.com/hong142/test/blob/master/20171030195012.png" width="250" height ="250"> &nbsp; &nbsp; &nbsp; <img src="https://github.com/hong142/test/blob/master/20171030202126.png" width="250" height="250"> <br />
Picture1:Intruder_positive &nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Picture2:Racoon_potential false positive <br />
<br />
The problem of **false positive** is a big concern for both the security service companies and the customers. At the very bottom level, if happened frequently, it will get customers annoyed and eventually hurt the trust and reputation of the service company. What’s worse, a false alarm almost always generates extra costs beyond the annoying alarm, due to the consequential responsive steps, including sending extra security guard or requsting emergancy service. Given the pervasive use of those systems, tremendous labors, time and money are wasted on false positive alarm each year. A pipeline with enough data corresponding to different motion objects respectively is of vital importance to improve the efficiency of the system while keep the high effectiveness, so that the system can make an intellectualized decision based on the data. 
## Potential Model
Below is a potential model to minimize the false positive problem. As you can see in the upper left corner of the picture, there is a probability (96%) and a name of object (raccoon). What we are trying to do is gathering large amount of data corresponding to common motion objects. And then when the new motion is captured, based on historical data we have in the model, we can assign a probability to different objects, predict the object by choosing the one with highest probability, and take further actions.<br />
<br />
<img src="https://github.com/hong142/test/blob/master/20171030221804.png" width="350">
### Model Limitation & Tradeoff
With this model, problems still exist when two probabilities are pretty close, but the solution really depends on the users' requirements that do we want to make action based on low probability or we only react to a probability higher than a certain level. Questions could also rise when unexpected or unusual things happened, such as people could break in with a raccoon costume, which might go out of the detection ability of our model. Since we believe those are rare exceptions and we are focusing on a more generalizable pipeline that can be used in scale production, we will not take this into consideration to build a complex analysis model. However, different users are expected to have different tradeoff senario. With significant benefits to justify the extra costs, there is always possibility to add another specialized pipeline to resolve the unique problems.
## Goals
We have three main goals to be achieved with the new pipeline:<br />
1. Gather image data for processing<br />
2. Process the image to detect the potential threats<br />
3. Organize and aggregate the data for consumption<br />
## Tech to use
We follow three general rules for selecting our tooling:<br />
1. Open source<br />
There is a huge trend to open source in industry due to its advantages, including lower costs and high flexibility.
2. Cloud native<br />
In this era, people tend to have the mindset of running things in a cloud environment.
3. Developed and used by the tech giant<br />
More reliable and easier to apply for those who want to replicate our pipeline.<br />
### Below are tools we are going to use, which can satisfy our pipeline goals and selection criteria:<br />
<br />
 

### Data Pipelining & Scheduling - [Docker](https://www.youtube.com/watch?v=RyxXe5mbzlU&feature=youtu.be), [Kubernets](https://www.youtube.com/watch?v=R-3dfURb2hA&feature=youtu.be),  [Pachyderm](http://pachyderm.io/)
During the second stage, data pipelining and scheduling, **containerization** will be used to improve utilization of server resource. Comparing to the traditional option of virtual machine, containers store libraries and dependencies in **application logic**, hence getting rid of the** operating system** as a necessary part to run an application. In other words, we can deploy multiple things on different machines without repeatedly installing operating systems, dependencies, model and data. The efficient and sustainable use of server resource, in turn, greatly reduces cost. <br /> 
<br /> 
Specifically, [**Docker**](https://www.youtube.com/watch?v=RyxXe5mbzlU&feature=youtu.be) will be used to interacts with operation systems to help create containers under developers’ discretion. The advantage of the docker system is that it removes the need of hypervisors, simplifies the usage of containers, and allows the interaction with containers created by others.<br /> 
<br /> 
Although containers simplified the deploy process, things can still mess up and lose track, especially when the scale becomes large enough. Instead of manually making decision about work distribution among servers, [**Kubernets**](https://www.youtube.com/watch?v=R-3dfURb2hA&feature=youtu.be), a platform working with containers, will be used to take care of scale deployment. Having its master node knowing all the server and container resources available, Kubernets dynamically provides the most efficient way of server allocation while fulfilling any hardware criteria specified by developers. In addition, Kubernets is capable of monitoring and auto-recovering on operation of application. Kubernets also enables data scheduling in terms of prioritizing certain requirements.<br /> 
<br /> 
Same as we mentioned in the data storage and origination part, just the completion of setting up of data pipeline is not sufficient to generate value for business. [**Pachyderm**](http://pachyderm.io/) is again needed to get things connected so that data can flow through the entire system.

### Analysis and Machine Learning - [Python](https://www.python.org/),[TensorFlow](https://www.tensorflow.org/)
The last piece of the project is analysis and machine learning. [**Python**](https://www.python.org/) will be the language simply because it's powerful in machine learning while of low complexity. [**TensorFlow**](https://www.tensorflow.org/) developed by google will be the key library, beacuse it is designed for dealing with large scale of data. In this project, our model will be graph based, and TensorFlow is good for building such complex model and runs fast.
## Summary
<img src="https://github.com/hong142/test/blob/master/20171031210948.png" width="600" >
<img src="https://github.com/hong142/test/blob/master/20171031211208.png" width="600" >
