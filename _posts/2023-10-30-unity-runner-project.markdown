---
layout: post
title:  "Unity Endless Runner Experiment"
date:   2022-11-19 12:46:00 +0200
categories: [Projects, College]
tags: [Unity]
image:
  path: /assets/img/unity-runner-project/unity_runner_project.png
  alt: An endless runner made with Unity. Complete with dangerous obstacles.
---
## Overview
INFO-I 304 is an Introduction to Virtual Reality (Unity) course that I took during my senior year in college. The scope of the course was extremely wide and went over everything from animations to UI elements. I really enjoyed getting the chance to explore all these tools as in depth as I could throughout the semester. 

By the time we got to the end of the semester, it was time for the final project. It was extremely open ended, with the only requirement being to use elements we learned throughout the semester to create our own custom Unity scene. I decided to use this project to push myself and try to create a simple game. I decided on an infinite runner style game as I read online that they're not too difficult to put together on a tight schedule, and I only had about two weeks to get this project going.

## Level Design
![Desktop View](/assets/img/unity-runner-project/223420.png){: width="100%" }
_Overview of full level. Very long single hallway._
I decided to make the actual level pretty simple. It's a 3 segment hallway with cap at each end to prevent the player from exiting the play space. There's a bunch of red lights lining the ceiling that slowly fade on and off to help give the impression that the players supposed to be in some kind of urgent situation. Of the entire 3 segment hallway, the player is only ever able to stand in the first and the very beginning of the second part.

### Why the weird layout?
One of the bigger challenges of building this game is making it infinite. There's a lot of different ways to accomplish this, but pretty much they all boil down to simply making the environment *look* like it's infinite. The layout of the current environment is meant to help give the impression that it's infinite even though in reality, its 3 hallway segments long.

### Teleportation!
![Desktop View](/assets/img/unity-runner-project/101345.png){: width="100%" }
_Overview of first segment._
Here you can see the first segment with the action planes visible. The orange one on the left is a teleport trigger and the blue one on the right is a teleport target. The planes are spaced the perfect distance apart so when the player is teleported, the surroundings look exactly the same relative to the player position. By doing this and locking the camera a fixed distance from the player on a rail-like system, we achieve a seamless infinite looking loop!

![Desktop View](/assets/img/unity-runner-project/103418.png){: width="100%" }
_A somewhat infinite hallway._

## Traps & Automatically Generated Obstacles
I've now got a infinite hallway which is awesome! Next step is to make some traps for this hallway to make it a bit more dangerous. The traps can vary wildly in how they present themselves and react to the player, but the one thing they all have in common is they will all cause a game over if you come into contact with one.

### Fire Fan
![Desktop View](/assets/img/unity-runner-project/124124.png){: width="215" .w-50 .left}
The fire fan is a static trap that doesn't move. It creates a pillar of fire originating from the ceiling for the player to dodge. The actual hitbox for the trap is a simple cylinder placed around the fire particle effect and stretches from the ceiling to the floor. Any contact with this hitbox results in a game over and the player would need to restart.

### Runaway Cart
![Desktop View](/assets/img/unity-runner-project/130608.png){: width="100%" .w-50 .right}
The runaway cart is an active trap that reacts to the player. It takes the form of 2 doorways perfectly across from one another. When the player gets close enough to the doorways, they'll hit an activation trigger which will cause a cart to come barreling out of the left doorway towards the right one. The doorways themselves are harmless, but the cart is dangerous and can cause a game over if the player comes into contact with it. The hitbox for this is a simple cubic shape as there isn't a need to get more specific than that. Fun fact, when I originally had the idea for this trap, I was thinking of a laboratory cart with vials and other things on it. When I went looking for appropriate assets on the Unity Asset store though, I found this medieval-looking wooden cart and thought it'd be fun to use that instead. I guess this laboratory you end up running through specializes in either wooden carts or time travel!

### The Wall
![Desktop View](/assets/img/unity-runner-project/111917.png){: width="300" .w-50 .left}
This one was just something I personally thought was funny. There's a door at the end of each hallway the player has to run through in order to continue the loop and keep playing. If you miss the door and hit the wall instead, it will cause a unique death animation to play and your game is over. Simple yet effective!

### Random Generation
The random generation of traps was relatively simple to implement. There's a fixed interval between when a new trap can be generated based off the players current position. Once that interval has been reached, a script looks through a list of all the potential traps it can pick. Based on the trap that is selected, there are some constraints on a per-trap basis. For example, when the fire fan trap is selected, it's allowed to generate up to 2 fire fans per interval and the constraint is that they must not be placed in the same horizontal position. If the runaway cart trap is selected, then it can only place 1 of those at any given time and only has 1 possible position it can be placed at horizontally. This is because if the script attempted to generate 2 at a time or adjust it's horizontal position, it would either be placed outside of the map or perfectly overlapping each other.

### Keeping Things Aligned
![Desktop View](/assets/img/unity-runner-project/133917.png){: width="100%" }
_Top down overview of randomly generated traps._
Since the player's position keeps getting reset to the beginning of the first hallway segment and traps are generated based on the players current position, I've created kinda a problem for myself. Traps will keep being generated in the first hallway segment until eventually, it's impossible to traverse the level anymore. In order to fix this problem, I implemented more teleportation! When the player gets teleported back to the beginning of the first hallway segment, so do all the traps relative to their distance to the player when they hit the teleport trigger. What this causes is for everything to move relative to the player making the game playable and infinite!

### Fixing Memory Issues
If I just left the current code as is, Unity would keep generating traps forever eventually causing the program to crash when it runs out of memory it can use. To prevent this issue, I implemented some code to delete traps once they get teleported outside the bounds of the map. This should only happen when the traps have already been run past by the player, so they shouldn't see this happening.

## Playable Game
> Click on the game window to gain control. Use `WASD` or `↑←↓→` to move around and avoid the obstacles.
{: .prompt-info }
{% include embed/endless_runner_game/endless_runner_game.html %}

{% include embed/shields/github_archive.html url='https://github.com/hypertacos520/I304-Unity-Runner-Project' %} {% include embed/shields/github_pages.html url='https://www.dawsonthompson.com/I304-Unity-Runner-Project/' %}

## Final Thoughts
As you may have seen when trying out the game, it's not without it's problems. For example, there is no way to reset your character if you run into an obstacle besides refreshing the webpage. If given more time, I think I'd like to add more variety of traps, some extra animations for the existing traps, fix the partical pop-in issue, and add a retry button. Overall though, I'm very happy with how this came out. It was a great game design experiment and I think it's definitely helped me learn a lot about how to build functional 3D environments.