# Threats Detection Data Pipeline
This document will introduce a general plan for building a threats detection data pipeline that works on the data from motion detectors of security systems. We will guide you through the problem that leads to the need for the pipeline, the goals we want to achieve with the pipeline, and the technologies we are going to use to build the pipeline.
## Problem
The motion detectors are deployed by many companies as well as individuals all over the world as part of their security systems to get timely alarms about intruders. Basically, a camera captures a series of images or videos, and then those images will stream into the top of certain pipeline to detect unusual motions. However, motion detectors can catch the motion of not only humans but also other non-human objects such as animals. For example, what the first picture cached is an obvious intruder, while things like the racoon in the second picture could also cause the alarm if there wasn’t any pipeline to further analyze the image data to identify the object in motion. In the first case, it's good that we can detect the motion, trigger the alarm and probably calling the police. But for the second case, if the system actually triggered the alarm because of the racoon, we will get what we called **false positive detection**, unless the customer do want to react to such "intruders" the same way as human intruders every time.  <br />
<br />
<img src="https://github.com/hong142/test/blob/master/20171030195012.png" width="250" height ="250"> &nbsp; &nbsp; &nbsp; <img src="https://github.com/hong142/test/blob/master/20171030202126.png" width="250" height="250"> <br />
Picture1:Intruder_positive &nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Picture2:Racoon_potential false positive <br />
<br />
The problem of **false positive** is a big concern for both the security service companies and the customers. At the very bottom level, it will get customers annoyed and eventually hurt the trust and reputation of the service company. What’s worse, a false alarm almost always generates extra costs beyond the annoying alarm due to the consequential reaction processes,including sending extra security guard or requsting emergancy service. Given the pervasive use of those systems, tremendous labor, time and money are wasted on false positive alarm each year. A pipeline with enough data corresponding to different motion objects respectively is of vital importance to improve the efficiency of the system while keep the high effectiveness, so that the system can make an intellectualized decision based on the data. 
## Model
Below is a potential model to minimize the false positive problem. As you can see in the upper left corner of the picture, there is a percentage of the probability (96%) and the name of most possible object (raccoon). What we are trying to do is gathering large amount of data corresponding to common motion objects. And then when the new motion is captured, based on historical data we have in the model, we can assign a probability to different objects, predict the object by choosing the one with highest probability, and make further actions.<br />
<br />
<img src="https://github.com/hong142/test/blob/master/20171030221804.png" width="350">
## Model Limitation
With this model, problems still exist when two probabilities are pretty close, but the solution really depends on the users' requirements that do we want to make action based on low probability or we only react to a probability higher than a certain level. Questions could also raise when unexpected or unusual things happened, such as people could break in with a raccoon costume, which might go out of the detection ability of our pipeline. Since we believe such things are extremely rare and we are focusing on a more generalizable pipeline model, we will not take this into consideration. However, for customers that have enough reasons to justify the need and cost, there is always possibility to add another specialized pipeline to resolve their unique problems. Tradeoff, development costs associated operational costs responsive steps.
## Goals of Model
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
<img src="https://github.com/hong142/test/blob/master/20171030225651.png" width="200" height ="200">&nbsp; &nbsp; &nbsp;<img src="https://github.com/hong142/test/blob/master/20171031153622.png" width="450" height="200"> <br /> <img src="https://github.com/hong142/test/blob/master/20171030225818.png" width="675" height="200"> <br />
### Data Storage & Organization -[S3](https://aws.amazon.com/s3/), [Pachyderm](http://pachyderm.io/)
Since our data is primarily images, which considered unstructured, we'll choose [**object storage**](https://blog.rackspace.com/introduction-to-object-storage) to organize data. Most importantly, object storage is much cheaper than other ways of storage and we are expecting large amount of data. Another advantage of object storage is its simplified management due to its flat container structure. Unlike block storage and file storage, which requires predefined structures and metadata, data are stored in buckets when we do object storage, so we don’t have to know the exact schema, including number, type, scale and structure of any block ahead of time. All we need is to dump the data and retrieve the data whenever necessary, which is more stable and user friendly. Because of such high abstraction, the development process with object storage is more flexible. Moreover, the high scalability provided by the object storage is of value typically under the security service context. <br />
<br />
In this project, we will use Amazon Simple Storage Service ([**S3**](https://aws.amazon.com/s3/)) as our storage cloud. In addition to the benefit mentioned above for object storage, S3 also has relatively higher reliability considering its wide usage among all kinds of companies and Amazon as its provider. <br />
<br />
On top of the object storage, we will need another data organization layer to perform **Data Versioning**, **Access Control**, and **Statistics**. [**Pachyderm**](http://pachyderm.io/) is our choice to realize those functions. It serves as the **interaction layer** between local machines and clouds, allowing us to organize data in repositories, track state and trigger analyses, and manage access to data under a unified way. Thus, most potential risks in data storage and organization regrading scale production, such as access control and privacy compliance, are taken care of. And this is extermely important to acheive for industries that deal with sensitive pravite data. <br /> 

### Data Pipelining & Scheduling - [Docker](https://www.youtube.com/watch?v=RyxXe5mbzlU&feature=youtu.be), [Kubernets](https://www.youtube.com/watch?v=R-3dfURb2hA&feature=youtu.be),  [Pachyderm](http://pachyderm.io/)
During the second stage, data pipelining and scheduling, containerization will be used to improve utilization of server resource. Comparing to the traditional option of virtual machine, containers store libraries and dependencies in **application logic**, hence getting rid of the** operating system** as a necessary part to run an application. In other words, we can deploy multiple things on different machines without repeatedly installing operating systems, dependencies, model and data. The efficient and sustainable use of server resource, in turn, greatly reduces cost. <br /> 
<br /> 
Specifically, Docker will be used to interacts with operation systems to help create containers under developers’ discretion. The advantage of the docker system is that it removes the need of hypervisors, simplifies the usage of containers, and allows the interaction with containers created by others.<br /> 
<br /> 
Although containers simplified the deploy process, things can still mess up and lose track, especially when the scale becomes large enough. Instead of manually making decision about work distribution among servers, ee, a platform working with containers, will be used to take care of scale deployment. Having its master node knowing all the server and container resources available, xx dynamically provides the most efficient way of server allocation while fulfilling any hardware criteria specified by developers. In addition, ee is capable of monitoring and auto-recovering on operation of application. Xx also facilitates data scheduling in terms of prioritizing certain requirements.<br /> 
<br /> 
Same as we mentioned in the data storage and origination part, just the completion of setting up of data pipeline is not sufficient to generate value for business, fsf is again needed to get things connected so that data can flow through the entire system.

### Analyze and ML - [Python](https://www.python.org/),[TensorFlow](https://www.tensorflow.org/)
TensorFlow is powerful in analyzing and is widely us
