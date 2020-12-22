+++
author = "Filip"
title = "Conduct a Fresh Environment Setup"
date = "2020-12-22"
description = "A post describing a way of automating your environment setup and maintenance."
tags = [
    "Ansible",
    "PowerShell",
    "WSL",
    "Windows Terminal",
]
categories = [
    "DevOps",
    "Orchestration",
]
series = ["Orchestration"]
aliases = ["migrate-from-jekyl"]
feature_image = "/images/avatar.png"
+++

# Conduct a Fresh Environment Setup
 
## Intro 
 
Long ranting following.
=======================
 
_Please feel free to skip at your own choice._
 
Why? Why not just install everything and be done with it. Just do whatever you need and want, set up your environment on a whim without any consequences. Well, that is a good question, and an answer would be that I decided to learn a new technology that would help me in my everyday work life. And the best way to start learning something is to try and find a way to apply it to one obscure corner of your personal or work life. And this dark unvisited corner was my personal OS setup. And as we are all aware this is a task that happens once in a full moon on a leap year. It is a daunting and seldom done task of necessity only reserved for times when you have some sort of a spiritual crisis and you question the stability of reality to try to redo your fragile and working environment. 
 
The first thing that popped in mind is to just set up everything by hand and be done with it. But at the same time, I wanted(you may also say needed) a way of keeping up all my history of changes during my long setup and installations without the need to try to rethink them each time I did something. I wanted to have the power to not remember, but write down. As the saying goes a somewhat smart person writes down, and the other(Who would know who this other person is) tries to remember. All this dark magic would do all my bidding, be the one that runs menial tasks, be the one that takes charge and be responsible for keeping the environment up to date(you might even say modern and in with the times). Make me follow all the new edgy things, even when my mind forgets. And let me tell you I do not care about following trends, but I do care when I am missing an improvement that would simplify or remove some manual task.
 
Imagine having not to be an operations person, but you are introduced to a culture that brings people together. Yes, just imagine that bringing people close, what dark sorcery is this. People, working closer and more open, blasphemy I say. You might say this sounds familiar, there are all these buzzwords out there, and cultures that promise the same. Well they promise and they deliver to some level, and this is just another buzz going around. But I say why not if we can find just a couple of small tricks or AHA moments that can do something for us in reaching our goals. 
 
Besides the feeling that I was on only one side of the coin, I wanted to see how this can be achieved with some of the automation tools that are out there and are open-source. And to be honest, starting to explore and expand on this new approach and tool. Made time and space stop. Well yes, why did it. It did because of this fun thing you have when you try to apply something you learned, something new in software engineering. Time stops because sometimes you have so much fun, and other times you get blocked and never notice that time around you is just passing by. While all the time you are stuck in this fishbowl circling like a little golden fish.
 
So let's get to it.
 
The End of the story
====================
 
## Get down to the nitty-gritty
 
_And yet a start of another shorter rant._
 
In a world of too much manual work, you need a conductor that will gather the misplaced manual laborers into a well-sounding orchestra and produce wonderful results. [Ansible](https://www.ansible.com/) is here to do the conducting, and gather all these different tools and approach and conduct them into a smooth sounding opera. 
 
A half automated way of orchestrating your local environment can be started by following some steps.
_Please do keep in mind that most steps if not all of them will require elevated rights._
 
We start by giving some more humph to our windows PowerShell by executing some basic commands.
First what we do is change our [Set-ExecutionPolicy](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-7.1).
```powershell
PS> Set-ExecutionPolicy Bypass -Scope Process -Force
```
Next, we need to do some stage setup and for this, we require to do some additional changes to our windows.
Enable the Virtual Machine Platform optional component on Windows. 
```powershell
PS> dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
 
### Install Windows Terminal
 
There are 3 different ways to install Windows Terminal:
 
- From the Windows Store
  - [Windows Terminal](<https://www.microsoft.com/de-de/p/windows-terminal-preview/9n0dx20hk701?activetab=pivot:overviewtab>)
- From GitHub
  - [Windows Terminal](<https://github.com/microsoft/terminal>)
- Using Chocolatey
  - [Windows Terminal](https://chocolatey.org/packages/microsoft-windows-terminal/)
 
Or you can leave it to the task included in the common role, which would install Windows Terminal for you. _For this you need to have a way to start the orchestra from some other Linux machine. Either a container or another machine._
 
After installing it you can set up a shortcut that would always run your Terminal in Administrator Rights. 
 
- Right-click on Desktop and create a new Shortcut
- In Location input: ```cmd  C:\Windows\system32\cmd.exe /c start /b wt```
- As Name input: Windows Terminal
- And as a final step, right-click into properties -> Advanced and check the option to "Run as Administrator"
 
### Windows Subsystem for Linux
 
This feature can be installed from the Ansible roles. But since this is a local setup using WSL itself. We would have to do it earlier, so we can use it to run the ansible-playbook.
```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
# Restart machine
# A distro version can be chosen from the list provided by MSFT.
PS> Invoke-WebRequest -Uri https://aka.ms/wsl-ubuntu-1804 -OutFile Ubuntu.appx -UseBasicParsing
PS> Add-AppxPackage .\Ubuntu.appx
PS> ~\AppData\Local\Microsoft\WindowsApps\Ubuntu.exe
```
After installing the distro version you can configure WSL 2 as a default version.
 
*At the moment WSL 2 doesn't provide an easy way to communicate with the windows host, and Ansible cannot be executed without temporary workarounds.*
```bash
wsl --set-default-version 2
```
 
#### Install Ansible and add additional packages within WSL
 
Install packages and update the distro with the following commands:
 
```bash
$> sudo apt-get update
$> sudo apt-get upgrade
$> sudo apt-get install socat
$> sudo apt-get install git
$> sudo apt-get install zulu-11
$> sudo apt-get install software-properties-common
$> sudo apt-add-repository -y --update ppa:ansible/ansible
$> sudo apt install ansible
```
 
### And what it boils down to, what the Conductor reads and conducts to the orchestra
 
After everything, all the torture in our little fishbowl, we get to hear what Ansible can do for us.
And it all starts with:
```bash
ansible-playbook freshinstall.yml -i hosts/inventory.yml
```
The residing spot of this example orchestra can be found in [AnsibleFreshInstall: Setting up a local environment from scratch, with an Ansible script.](https://github.com/Filip3/AnsibleFreshInstall).
 
_Please check the readme of the project. It might contain hidden added details._