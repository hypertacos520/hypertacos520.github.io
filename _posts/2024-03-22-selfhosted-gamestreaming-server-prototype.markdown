---
layout: post
title:  "Selfhosted Gamestreaming Server Prototype"
date:   2024-03-22 1:30:00 +0200
categories: [Projects, Selfhosted Gamestream Service]
tags: [Virtual Machine, Networking, Streaming, KVM]
---
## Overview
Ever since I was young, I was really interested in the idea of gamestream as a technology. The idea that you could play a home console quality game on something like a smartphone with minimal battery impact and low latency was basically like magic at the time. Of course, it didn't quite work out that way in practice back then. The standard for mobile infastructure was 3G or 4G if you were lucky, home networks could very commonly have a grand total of 2mb bandwith to use for the entire house, and consumer grade PCs in general weren't all that reliable.

Fast forward to today and not only are most of these problems resolved, but there's many different centralized options for gamestreaming that are somewhat affordable. Not only this, but there's even some devices being tailor made specifically to access some of these services and provide the best experience possible. This is fantastic! The barrier to entry for gaming has never been lower than it has today and I absolutely love it.

### Closing the Open Doors
There is a change slowly happening in the gaming industry that I've noticed. The bigger companies are attempting to create a walled garden style ecosystem to make it more difficult to leave a given platform once you've invested in it. This makes a lot of sense considering many gaming platforms are starting to become hardware agnostic, but what if you want a more open alternative that works with any device you decide to link it to?

## Streaming Protocol
There's been a lot of work put into this technology by small startups and the open source community long before it became as commonplace as it has today. As a result, theres a number of different clients available for selfhosting your own game streaming service! That being said, it isn't necessarily a simple task. Many of these solutions have the actual streaming part working great, but other parts of the user experience are tailored for different usecases.

For example, [Rainway](https://en.wikipedia.org/wiki/Rainway) was a small startup company that focused on optimizing a low latency streaming experience for gamestreaming. They had native apps for most platforms, as well as an HTML5 based webplayer that worked extremely well. Later, the company shifted the technology to be more of a remote desktop-style software completely leaving behind it's origins of being a gamestreaming service. You could absolutely still use the platform for gamestreaming and it was still very good for that, but a lot of the ux had changed at that point resulting in a less fluid experience. Many other smaller companies have similarly pivoted their service over the years, but that doesn't mean all options are limited to this more corporate usecase.

### Gamestream Protocol
![NVIDIA Shield Portable](/assets/img/cc-nc-img/8357278985_5e68fba01e_o.png){: width="auto" .w-25 .left}
One of the original services that offered a seamless streaming experience was [NVIDIA Gamestream](https://www.nvidia.com/en-us/support/gamestream/). The service was intended to stream games from a NVIDIA GPU powered PC to a [Shield device](https://www.nvidia.com/en-us/shield/). A few different Shield devices were made including the Portable (as seen on the left), Tablet, and Android TV box. As of the current year, development on the shield project has slowed down and the Gamestream service provided for users to connect to their own PCs has been discontinued. The gamestream protocol that was developed by NVIDIA, however, lives on in the form of Sunshine & Moonlight. 

_"[Shield Portable from NVIDIA Project SHIELD](https://www.flickr.com/photos/13815526@N02/8357278985)" by [Pierre Lecourt](https://www.flickr.com/photos/13815526@N02/) is licensed under [CC BY-NC-SA 2.0](https://creativecommons.org/licenses/by-nc-sa/2.0/)[^shield-portable]_

### Sunshine & Moonlight
[Moonlight](https://moonlight-stream.org/) is an alternative client for the Gamestream protocol that can be used on (almost) any device. This already opened up the protocol a lot but more recently a new open source project has appeared. [Sunshine](https://docs.lizardbyte.dev/projects/sunshine/en/latest/about/overview.html) is an alternative server for the Gamestream protocol and works on not only NVIDIA GPU based machines, but any machine that's capable of running high end games on most of the major desktop operating systems. 

These two projects have cracked the protocol wide open and made it extremely versatile! 

## Building the Host Environment
We now have our protocol we'll be using to develop this gamestreaming server, but now we have to make some decisions about the host environment. The OS in particular was a difficult choice. With all the work Valve has done for gaming in the Linux world, basing the host off this OS would be great since it would rely a lot on open source and custom software. With Windows, you gain a massive amount of compatibility. Most games were built for windows to begin with, so they tend to run the best on that platform. On top of this, you also gain access to Xbox games native on the machine which is a big plus if you're invested in that ecosystem.

Ultimately, I decided to go with Linux for it stability when it comes to running custom software. Spinning up a linux virtual machine is easy enough, and most things run without issue through Valve's Proton compatibility layer.

### Virtualization
Virtualization is usually not something you'd want to do for a gaming system, but it's something I wanted to experiment with. There's a few benefits to virtualizing this system, but the main one is that you can run your gamestreaming server as just another service on a larger system. This means you can in theory run more than just this on the hardware while keeping each service isolated from one another. 

![KVM VM](/assets/img/gamestreaming-server-prototype/kvm-vm.png){: width="auto" height="auto" .left .w-50}
A typical downside to virtualization is a loss in performance, however, it all depends on the kind of technologies you use. To gain a large amount of performance in a virtual environment, we can use a [KVM based virtual machine](https://linux-kvm.org/page/Main_Page). This kind of VM uses a lot of modern virtualization technologies and has a very low overhead resulting in near native performance. This technology also benefits from PCI-E passthrough, which means that an entire PCI-E device can be isolated for use exclusively by a VM. In my case, I'll be using a high end graphics card as the isolated PCI-E device. 

Initial tests with this virtualization tech are very promising for this usecase but also a bit unstable. Given that this is just intended to be a prototype, this will be good enough for testing. 

## Session Management
The Sunshine gamestreaming host manages the session all on its own and does a great job for the most part. An issue is introduced when you use a modern pc gaming platform with drm protections such as Steam. These platforms don't allow for sunshine to properly track the executable of the currently running application. This is because when you launch the game executable, it instead first launches steam. This is usually ok, but because of the way sunshine works, you won't be able to properly manage the session for these applications. To help solve this issue, I've developed a short python script with a few different functions to help Sunshine to be able to properly close these kinds of games when requested.

### Steam Game Tracking

```python
import psutil

def get_pid_by_name(game_name):
    words = game_name.split()
    
    for word in words:
        found_processes = []
        for process in psutil.process_iter(['pid', 'name']):
            if word.lower() in process.info['name'].lower():
                found_processes.append(process)
        
        if len(found_processes) == 1:
            return found_processes[0].info['pid']
        elif len(found_processes) > 1:
            print(f"Multiple processes found for '{word}'. Please provide more details.")
            return None
    
    print(f"No matching process found for the given name.")
    return None 
```
{% include embed/shields/github_repo.html url='https://github.com/hypertacos520/Gamestream-Server' %}

This function attempts to find a game's process id (pid) based on it's name. It trys to be as general as possible while not targeting multiple processes at once. If it finds more than one process with a given name or doesn't find it at all, it will return None. If it does find a result with only one process, we assume it's found the pid of the game and returns it.

The resulting process ID can be terminated using a clean exit function.

```python
import os, signal, time

def terminate_process(pid):
    try:
        # Send the SIGTERM signal
        os.kill(int(pid), signal.SIGTERM)
        print(f"Process with PID {pid} terminated successfully.")
        
        # Check if the process is still alive after SIGTERM
        time.sleep(1)  # Give some time for the process to respond to SIGTERM
        try:
            os.kill(int(pid), 0)  # Check if the process is still alive
            print(f"Process with PID {pid} is still alive. Sending SIGKILL.")
            os.kill(int(pid), signal.SIGKILL)  # Send the SIGKILL signal
            print(f"Process with PID {pid} forcefully terminated.")
        except ProcessLookupError:
            print(f"Process with PID {pid} not found after SIGTERM.")
    except ProcessLookupError:
        print(f"Error: Process with PID {pid} not found.")
        exit(-1)
    except PermissionError:
        print(f"Error: Permission denied to terminate process with PID {pid}.")
        exit(-2)
    except:
        print(f"Error: Process {pid} failed to terminate. Unknown Error.")
        exit(-3)
```
{% include embed/shields/github_repo.html url='https://github.com/hypertacos520/Gamestream-Server' %}


This function will first attempt to cleanly close the process. If that fails, it will fallback to a force close operation. If the process is closed successfully, then the function will simply end. If the process fails to close, it will throw a program error so that sunshine knows something went wrong. 

This method is great as long as the game name matches the process name, but what if it doesn't? There's a few different games that do not have a matching process name. One of the most common examples of this is Half Life 2 which has an executable name of "hl2.exe". For these specific scenarios, we need a fallback method of termination.

```python
import pyautogui

def force_terminate_process():
    pyautogui.keyDown('alt')
    pyautogui.press('f4')
    pyautogui.keyUp('alt')
```
{% include embed/shields/github_repo.html url='https://github.com/hypertacos520/Gamestream-Server' %}


This force terminate function is used only as a fallback method. It's a bit barbaric in that it simulates keyboard input to trigger the hotkey to force exit an application, but it does work. In theory, the only application that should ever be in focus that would react to that keybind is the running game, so it should be ok. If you go to deploy this code on a computer you use for anything other than a dedicated gamestreaming environment, then it could be problematic.

## Conclusions
Initial results of the full Gamestreaming environment are very promising! There's still some weirdness to work out, for example sometimes the sunshine service will fail without automatically restarting itself. The virtual machine environment itself seems somewhat unstable, but fixable. More work will need to be done to ensure this kind of environment is usable and easily deployable.

## Sources
[^shield-portable]: "[Shield Portable from NVIDIA Project SHIELD](https://www.flickr.com/photos/13815526@N02/8357278985)" by [Pierre Lecourt](https://www.flickr.com/photos/13815526@N02/) is licensed under [CC BY-NC-SA 2.0](https://creativecommons.org/licenses/by-nc-sa/2.0/)