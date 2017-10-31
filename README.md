# Threats Detection Data Pipeline
This document will introduce a general plan for building a threats detection data pipeline that works on the data from motion detectors of security systems. We will guide you through the problem that leads to the need for the pipeline, the goals we want to achieve with the pipeline, and the technologies we are going to use to build the pipeline.
## Problem
The motion detectors are deployed by many companies as well as individuals all over the world as part of their security systems to get timely alarms about intruders. Basically, a camera captures a series of images or videos, and then those images will stream into the top of certain pipeline to detect unusual motions. However, motion detectors can catch the motion of not only humans but also other non-human objects such as animals. For example, what the first picture cached is an obvious intruder, while things like the racoon in the second picture could also cause the alarm if there wasn’t any pipeline to further analyze the image data to identify the object in motion. In the first case, it's good that we can detect the motion, trigger the alarm and probably calling the police. But for the second case, if the system actually triggered the alarm because of the racoon, we will get what we called **false positive detection**, unless the customer do want to react to such "intruders" the same way as human intruders every time.  <br />
<br />
<img src="https://github.com/hong142/test/blob/master/20171030195012.png" width="250" height ="250"> &nbsp; &nbsp; &nbsp; <img src="https://github.com/hong142/test/blob/master/20171030202126.png" width="250" height="250"> <br />
Picture1:Intruder_positive &nbsp; &nbsp; &nbsp;&nbsp; &nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Picture2:Racoon_potential false positive <br />
<br />
The problem of **false positive** is a big concern for both the security service companies and the customers. At the very bottom level, it will get customers annoyed and eventually hurt the trust and reputation of the service company. What’s worse, a false alarm almost always generates extra costs beyond the annoying alarm due to the consequential reaction processes,including sending extra security guard or requsting emergancy service. Given the pervasive use of those systems, tremendous labor, time and money are wasted on false positive alarm each year. A pipeline with enough data corresponding to different motion objects respectively is of vital importance to improve the efficiency of the system while keep the high effectiveness, so that the system can make an intellectualized decision based on the data. 
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
Below are tools we are going to use, which can satisfy our pipeline goals and selection criteria:<br />
<img src="https://github.com/hong142/test/blob/master/20171030225651.png" width="250" height ="250">&nbsp; &nbsp; &nbsp;<img src="https://github.com/hong142/test/blob/master/20171030225855.png" width="250" height="250"> <br /> <img src="https://github.com/hong142/test/blob/master/20171030225818.png" width="600" height="250"> <br />
### Data Storage -S3
Since our data is primarily images, which considered unstructured, we'll choose **object storage** because it has higher scalability, durability and lower costs. Unlike block storage and file storage, data are stored in buckets, so we don’t have to know number, type, scale and structure of those blocks ahead of time. We also don’t need teams of people to manage the database before data comes in and ready for consumption. Specifically, we will use Amazon Simple Storage Service (**S3**).<br />
Object storage: https://blog.rackspace.com/introduction-to-object-storage<br />
S3: https://aws.amazon.com/s3/<br />
### Scheduling and pipelining - Kubernets, Docker, Pachyderm
On top of the object storage, we will also use **Data Versioning**, **Access Control**, and **Statistics**.<br /> Generally, they allows us to organize data in repositories. They also track state and trigger analyses, and manage access to data.<br /> As a result, we in some extend avoid the problem of some legislation responsibility to clearly keep track of what we did on and get from certain persons' data.
Data pipelining has setting up different piece so they flow into one another, and triggering those pieces when needed in variety stages and on variety platforms. By having the pipeline, we can have systems look like in a certain way, and things in certain containers and all of those containers can now run on a common engine. It’s easy to move certain part to a different environment without too much trouble.
### Analyze and ML - TensorFlow
TensorFlow is powerful in analyzing and is widely used
