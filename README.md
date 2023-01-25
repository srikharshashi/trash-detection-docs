# Trash Detection and Reporting using Yolov5,ServiceNow and Drone Kit

## Problem Statement
We aim to provide the higher authorities responsible for sanitation and cleaning of large areas or intuitions a method for evaluation the work of their subordinates without manually inspecting the areas which might be kilometers apart.
## Solution

 - Aerial Images taken using a drone will be inferred via a customyolov5 model which was trained on images of trash 
- If the number of detections are more than a set threshold an alert is sent to the authorities in the form of an Incident in ServiceNow 
 - The inferred images are also stored in Amazon S3 with a reference of them in the database for reference
 - The images corresponding to a given flight can be viewed via our Web Client

## Workflow

![Untitled Diagram drawio](https://user-images.githubusercontent.com/37980605/214506899-a3f0d6de-01a0-4539-91e3-8eafe1740409.png)

## Drone Command App
 - The Drone Command app can do the following tasks 
	 1. **Add new missions** :- It uses **GoogleMaps API** to facilitate the user to add new missions for the drone to perform and also stores them in MongoDB
	 2. **Command the drone** :- The app uses **WebSocket API** to command the ground station to perform a flight based on the mission chosen by the user 
	 3. **Live Tracking** :- The user can track the drone on a map as it completes it's missions to track it's progress live using the same Web Socket connection.
- Tech Stack :- Flutter,Websockets API,REST API
- Repository :- https://github.com/srikharshashi/drone-control
	 ![dronecomp](https://user-images.githubusercontent.com/37980605/214494531-186082d2-e97d-4e36-ad01-c53c21c85e77.png)

## Dronekit
- DroneKit is a python API for communicating with the ground station and UAV's which wraps over the ArduPilot API for flight control leveraging the **MAVLINK** protocol
- The Flight Controller we used (PixHawk) can communicate with the Onboard Computer as well as the Companion Computer on the drone.
- The Onboard Computer enables the drone to click pictures programmatically when a way point is hit 
- Camera used: GoPro Hero 9 Black
- The Ground station connects to the main server with a WebSocket connection 
	1. Its listens for LAUNCH command 
	2. It broadcasts it's location and it's update it's arming status to the status
- Once a mission is completed it creates a flight in the server for the given mission id
- The **flight_id** returned is then used for future purposes in Trash Detection Client
- Simulator used : Ardupilot Simulator (for local development)
- Tech Stack :- Ardupilot SITL,Dronekit SDK,Websockets using asyncio
- Repository :- https://github.com/srikharshashi/dronekit_websockets
![simulator](https://user-images.githubusercontent.com/37980605/214494436-36776372-c267-40e9-9769-833dc1604ad1.png)
## Trash Detection Client 
- Trash Detection Client is a GPU enabled client for detecting trash from the images obtained by the drone 
- The script which is run on a folder of images accepts a flight id and performs inference on those images and updates the flight details in the database based on number of inference
- The custom yolov5 trained model has an accuracy for 56% 
- It also uploads these images to Amazon S3 and preserves a reference URL to them 
- And finally after inference is done it creates an incident  in Service Now 
![image](https://user-images.githubusercontent.com/37980605/214497693-bda77114-072e-48ad-bcc1-071cf232c6a6.png)

![image](https://user-images.githubusercontent.com/37980605/214499109-d80fb8c6-fa17-48bb-a404-2c16d8e2c543.png)

- Tech Stack :- Yolov5,REST API
- Repository :- https://github.com/srikharshashi/projectschool
- Data Set Used :- https://universe.roboflow.com/alexandros-petkos/marinelitter-4k-test

## Backend

- The backend is an expressjs server with a MongoDB isntance hosted on Atlas .
- The hosting used is Amazon EC2 Free Tier paired with Amazon S3 as an iage storage service 
- It acts a CRUD server as well as a WebSocket Server 
- Resources 
	1.  Missions 
	2. Flights 
	3. Images 
- It's responsible for the fetching the above resources ,location tracking as well as drone command 
- Repository : https://github.com/srikharshashi/Mission-Store

## Web Application 
- A react application is used to view the infered images stored in the DB
- All the images corresponding to a given flight id can be fetched and viewed on the webapp
![image](https://user-images.githubusercontent.com/37980605/214505663-65b1518e-de41-428a-95d2-c313fbebcac2.png)
![image](https://user-images.githubusercontent.com/37980605/214505814-a17b0bf1-5d0d-4c44-afc7-39705075c74f.png)
- Repository :- https://github.com/Prudhvi472/Swach-Campus-Ngit
- Author :- Prudhvi Reddy 
