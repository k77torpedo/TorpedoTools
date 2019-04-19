#### DISCLAIMER
##### Using this project in any test- or productive-environment is at your own discretion!
##### This project is still in heavy developement and large parts may change at any point in time.

---
# Table of contents 
[1. Introduction](#introduction)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.1 What is it?](#what-is-it)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.2 Demonstration](#demonstration)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.3 When to use it?](#when-to-use-it)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.4 When not to use it?](#when-not-to-use-it)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.5 Features](#features)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.6 Installation and Setup](#installation-and-setup)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[1.7 Overview](#overview)

[2. How to use](#how-to-use)

[3. Best Practices](#best-practices)

[4. Unity Limitations](#unity-limitations)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[3.1 NavMeshes and SceneOffset](#navmeshes-and-sceneoffset)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[3.2 Static GameObjects and SceneOffset](#static-gameobjects-and-sceneoffset)

[5. FAQ](#faq)


# Introduction
## What is it?
This project is an alternative implementation of the standard `NetworkManager` that comes out-of-the-box with Forge Networking Remastered as an attempt to provide functionality like a persistent world, dungeon instancing or the concept or 'rooms' in one or more servers.

## Demonstration
![Demonstration](https://raw.githubusercontent.com/k77torpedo/ForgeAndUnity/master/Documentation/demonstration.gif)

## When to use it?
* You need your game to be split up into smaller parts and/or want to be able to run one game on multiple servers. 
* You want your clients to only connect to and see one part of the world instead of everything. 
* You want functionality like a persistent world or dungeon instances

## When not to use it?
* If you have no prior experience with Networking or Forge Networking Remastered. Gain experience with the framework first.
* If your project is an arena- or lobby-style game stick with the default `NetworkManager`. 
* If your project wants to have the host also be a player of the game. This solution is made for a "server" that clients connect to. 

Additionally, at the time of writing the native Steam-Integration of the standard `NetworkManager`, the standard implementation of a `MasterServer`, compatability with the `Webserver` or `Matchmaking` in Forge Networking Remastered are not integrated. While these features might be implemented at a later time please know that you will need to provide them yourself currently.

Be aware that if you want to use this over the standard `NetworkManager` or not depends on the scope and features of your own project and is at your own discretion.

## Features
_Info: the term "NetworkScene" describes a Unity-Scene with a `NetworkManager` that is only handling the `NetworkBehaviors` in that Unity-Scene._

* A Scene-based `NetworkManager` for easy creation of `NetworkScenes*`
* Multiple `NetworkScenes*` per Server-Instance
* Multiple Server-Instances (Can run the first 5 `NetworkScenes*` of your game on "Server_1" and another 3 `NetworkScenes*` on "Server_2")
* Provides extendable interconnection between Server-Instances out-of-the-box without a database
* Supports creating `NetworkScenes*` from one Server in another Server
* Supports instantiating `NetworkBehaviors` from one Server in another Server
* Clients can be instructed to change `NetworkScenes*` by the Server
* Concept of "Static-Scenes": `NetworkScenes*` that are and should always be reachable under a certain IP and Port, basically the "static  world"/"overworld"
* Concept of "Dynamic-Scenes": `NetworkScenes*` that are created on demand for things like Dungeon Instances or Player Housing Instances etc. and that will be destroyed again at some point
* Port-recycling for "Dynamic Scenes": If a "Dynamic Scene" is destroyed the port can be reused at a later time from a range of allowed Ports
* A global registration system for "Dynamic Scenes" that all servers can lookup connection information in and for preventing name-collusion of `NetworkScenes*` across Server-Instances
* Creating `NetworkScenes*` with a position-offset to prevent them physically overlapping each other
* `NetworkScenes*` can try to reconnect/rebind after a set delay when disconnected

## Installation and Setup
I recommend at least Unity 2018.3.6f1. If you have issues try upgrading to this or a higher version of Unity.

1) Create a new and empty Unity-Project.

2) Import the latest version of the "Forge Networking Remastered"-unitypackage into the empty Unity-Project from the official GitHub-Page found here: https://github.com/BeardedManStudios/ForgeNetworkingRemastered

2) Download the `ForgeAndUnity.unitypackage` from here: https://github.com/k77torpedo/ForgeAndUnity/tree/master/UnityPackages

3) Import the `ForgeAndUnity.unitypackage` into the project.

4) Register the Unity-Scenes found in the MultiServer-Example-Project in the BuildSettings and in the order shown below: 

![Setup Image](https://raw.githubusercontent.com/k77torpedo/ForgeAndUnity/master/Documentation/ForgeAndUnity%20Setup.JPG "Setup Image")

5) You need to recompile the `NetworkBehaviors` in Forge Networking Remastered in order for the `NetworkBehaviors` to work. To do so you simply need to save any `NetworkBehavior` in the Network Contract Wizard as shown below:

![Recompile](https://raw.githubusercontent.com/k77torpedo/ForgeAndUnity/master/Documentation/ForgeAndUnity%20Compile%20Forge.jpg "Recompile")

6) Open the `Login`-scene in the Unity-Editor.

7) Press Start!

## Overview
Click on the images below to enlarge.

![Overview abstract](https://raw.githubusercontent.com/k77torpedo/ForgeAndUnity/master/Documentation/ForgeAndUnity%20Overview.jpeg "Overview abstract")

![Overview classes](https://raw.githubusercontent.com/k77torpedo/ForgeAndUnity/master/Documentation/ForgeAndUnity%20Classes.jpeg "Overview classes")


# How to use

# Best Practices
## Best Practice #1: Prefix your Unity-Scenes
At any time and especially during scene-creation all Unity-Scenes must be named unique. Please prefix your Unity-Scene-Files so name-collision can be avoided. Instead of '_Level_1_' use '_template_Level_1_' or '_t_Level_1_' as this ensures there are no name-collisions during scene-creation that may cause unexpected behavior.


Explanation:

To create a new `NetworkScene` the framework will first create an _empty_ Unity-Scene with your desired scene-name (here 'Level_1') and put a `NetworkSceneManager` in it so it can be connected to.

Then a second - _the actual_ - scene will be created with the BuildIndex provided from the Unity-Scene-File and be merged with the other scene we created previously. This new scene will also be named 'Level_1' - in accordance to the Unity-Scene-File-Name. Now we have a name-collision. We have 2 Scenes with the name 'Level_1' that can't be merged because they both have the same name.

# Unity Limitations
## NavMeshes and SceneOffset
_Info: The `NetworkSceneTemplate.SceneOffset` allows a scene to be created with an offset so that it does not physically overlap with existing scenes._

Be aware that when you create a dynamic scene like a new dungeon instance or a player housing instance that the `NetworkSceneTemplate.SceneOffset` will not move the NavMesh associated with the scene. I recommend using the NavMeshTools from Unity to be able to create NavMeshes during runtime solve this problem.

## Static GameObjects and SceneOffset
_Info: The `NetworkSceneTemplate.SceneOffset` allows a scene to be created with an offset so that it does not physically overlap with existing scenes._

Be aware that when you create a dynamic scene like a new dungeon instance or a player housing instance that the `NetworkSceneTemplate.SceneOffset` can't be properly applied to the Unity-Scene when the `GameObjects` of the Unity-Scene are marked as `static`.

# FAQ


# Todo (Please bare with me :) )
- What are services? How to make a service?
- How are the Nodes communicating with each other?
- Unity-limitations to keep in mind (Static GameObjects + NavMeshAgents and NavMeshes)

## More to come :)


# MIT License
Copyright (c) 2018 k77torpedo

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
