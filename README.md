# Real-Time-Object-Detection-using-Cloud-and-Edge-Computing

Developed an application that provides real-time object detection utilizing both cloud (using AWS) and edge (using Raspberry Pi). Used a 
lightweight deep learning framework Darknet as object detection model, Amazon EC2 for performing object detection, S3 for storing object 
detection results, and Amazon SQS to couple various components in the system.

In this project, the idea of using both cloud computing and edge computing in tandem is implemented. We have done real-time object detection using Raspberry Pi that records the videos using its attached camera. We have used a lightweight deep learning framework,
Darknet as our object detection model that can run on either the pi or cloud. The objective of this system is to minimize the end to end latency of object detection, i.e the time between motion detected, video getting recorded, processed and the result is produced.
Another important factor is resource utilization, the cost of processing on the cloud must be minimized and the resources must be 
relinquished when not required.

This repo contains 4 files - 
1. surveillance.py
2. get_video.py
3. send_video.py
4. autoscaling.py

1.surveillance.py - This file takes the input from the motion sensor. If the motion is detected the sensor prints “Intruder detected” and starts recording
the video for 5 seconds and makes a subprocess call to send_video.py. This file runs on Raspberry pi which records the video when a motion 
is detected.

2. get_video.py - This file has 3 functions

    2.1 getjobs - gets one message stored in the sqs queue

    2.2 process - downloads the video from the s3 bucket using the key stored in the SQS and processes the video, parses the output to get the object detec

    2.3 parseoutput - parse the output returned by the darknet and stores the unique objects detected throughout the video as a list. This 
    file runs on every Ec2 instance that processes a video and stores the results in the s3 bucket by parsing the output returned by the YOLO model.

 3. send_video.py - This file takes the input video recorded by the camera, and uploads the video to the S3 bucket. This file runs on the pi and uploads the video to the s3 bucket
 
 4. autoscaling.py - This file contains the code for performing autoscaling. The functions in this file - 
 
    4.1 getLengthOfQ - computes the length of the queue
    
    4.2 getRunningIds- returns the list of instances that are running and are different from the master node. (As the master node runs all the time)
    
    4.3 getStoppedIds - returns the list of instances that are in stopped state and are different from the master instance
    
    4.4 processVideo - processes the video by running the darknet code.
    
    4.5 main - gets the length of the queue and list of instances that are in running state and stop state.
    
    This file runs on the master node which divides the work among the free instances based on the messages in the queue thereby ensuring that no instance is being underused.
    
    
  Installation -
  
  Python libraries required to run the above program are:
  
  1. Boto
  2. Boto3
  3. Paramiko
  
  The command for installing these libraries is:
      
      pip install boto boto3 paramiko

Note: Python version used for running these programs is 2.7.13

How to Run the Programs:

For Raspberry Pi

We run the surveillance.py using the command ‘python surveillance.py’.

For Master EC2 Instance

Here, we have written a python script 'autoscaling.py' which requires two arguments: AWS SQS URL and AWS Region Name.

The command is written as ‘python autoscaling.py <SQS URL> <AWS Region Name>’

