---
layout: post
title:  "Digital Representation of Home"
date:   2021-12-05 3:15:00 +0200
categories: [Classes, SOAD-S 210 - Intro to Digital Art]
tags: [Unity, Fusion 360, ProBuilder]
image:
  path: /assets/img/s210-home-project/002514.png
  alt: View of main island. Ominous beans stand in the background.
---
## Overview
Intro to Digital Art is a class that explores various digital tools that can be used to be creative and make things that can be shared in a digital fashion. For this project, the topic was to build a representation of what home means to us in a Unity environment.

### Original Project Proposal
> "When I think of home, one of the first things that comes to mind is the people I know and talk to frequently. The approach I’d like to take with this project is that it doesn’t really matter what the surrounding environment looks like as long as those people are present. My plan for this project is to create a physical environment that you’d never see in real life, leaning heavily into the “This is a computer-generated environment” aesthetic. Everything will be polygonal with simple color schemes. My visual interest will come primarily from environmental lighting. The lighting will be kind of crazy compared to the rest of the environment. The thing that will remain constant throughout the scene is the presence of people / animals. They will be scattered throughout this weird environment with their own thoughts and interactions. The interactions with the people with the different people will be scripted myself so they can be a little more dynamic and interesting."

## Building the World
![Desktop View](/assets/img/s210-home-project/151929.png){: width="300px" .w-50 .left}
Using ProBuilder, I created this 4 island layout with a few crystalized staircases connecting each one together. The islands themselves were meant to represent how I had stretched my home to include 4 physical locations by the time I made it to college. A lot of the other geometry you can see on the islands such as the house and park bench were made using Fusion 360 and appropriately scaled relative to the player.

### Intentional Weirdness
Within this environment, there's bioluminescent cubic trees, moving crystalized archways, and these cubes flying from the bottom and top of the world for infinity. All of this was intended to create the feeling of "this is very much a strange place I've never been to before". 

### The... Beans?
![Desktop View](/assets/img/s210-home-project/153253.png){: width="320px" .w-50 .left}
Ah yes the beans. The original design of these bean people was a result of the limitation of my skill at the time, but I ended up really liking the idea after making an initial prototype. Usually, you would use a program like Blender to make character models, but I haven't had that experience yet. In first-person game design, you would usually use this capsule shaped object to represent the player. I decided to take some creative liberties and use this capsule to represent all the people in my world. I gave them eyes and a large prominant article of clothing based on feedback I got from the real people I intended to represent with these little guys. This resulted in a bunch of people with unique designs & personalities.

### Scripted Interactions
![Desktop View](/assets/img/s210-home-project/153634.png){: width="100%" }
_Bean person social interaction._
To help bring the world to life, I added some scripted interactions. By pressing `left click` on the beans, you can talk to them! Each bean person has a few unique lines they can say and have their own personality. I personally really liked this addition since I think it helped make an otherwise very strange world feel familiar. This familiar feeling in my mind comes from simple social interaction.

## Finished World
> Click in the window to gain control. Use `WASD` or `↑←↓→` to move around and explore the world. To regain mouse control, press the `esc` key.
{: .prompt-info }
{% include embed/s210-home-project/s210-home-project.html %}

{% include embed/shields/github_archive.html url='https://github.com/hypertacos520/S210-Home-Unity-Project' %} {% include embed/shields/internal_site.html url='https://www.dawsonthompson.com/S210-Home-Unity-Project/' %}

> Sometimes this embedded player will display an old build of the project somehow. If you'd like to ensure you're trying the newest version, you can try the [`Internal Site`](https://www.dawsonthompson.com/S210-Home-Unity-Project/) link.
{: .prompt-warning }