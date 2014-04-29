---
layout: post
title:  Continuous Delivery on Windows, Part III
subtitle: Infrastructure Automation - Goodbye GUI madness
date:   2014-02-01
author: Rachel
categories: P2
---

And now we’re onto the really tough problem of automating the creation and updates to your infrastructure on Windows. What we want is to be able to create any environment at the touch of a button and using automation this environment magically appears before our eyes ready to deploy our software to. This allows us to do things like parallel testing and scaling of your infrastructure. The best way to do this at present is through virtualisation.

##Virtualisation

Virtualisation means you won’t be building all your environments from scratch on one box. Through shared use of server resources you can create lots of production-like environments at low cost. You can choose to build your own local cloud solution for this or choose a hosted option. The easiest is the latter, as most hosted cloud options provided by vendors like Azure from MS and EC2 from Amazon, provide Infrastructure as a Service (IaaS). This will give you some basic Windows infrastructure, like your operating system, but that is only part of the problem solved.

>“you can build the entire environment from scratch using Puppet and Chef…but hold on I thought we were on Windows?”

-----------------

##Environments

Most real environment instantiations are heterogeneous, which means they need to be configured for specific purposes. Examples of the types of environments you might need are development, continuous integration (CI) and production-like. Development is likely to have development tools and stubs to external components and applications. CI may only include your build tool and basic frameworks like .NET, but won’t need development tools or real external components and applications. Production-like environments will have real external components and systems, but won’t need build and development tools. You could start with a base image and then use configuration management tools like Puppet and Chef to configure your environment to your needs. Alternatively, you can build the entire environment from scratch using Puppet and Chef…but hold on I thought we were on Windows?

##Configuration Management

We are on Windows, and fortunately massive improvements have been made to configuration management tools like Puppet and Chef. They now have a lot of built in support for Windows packages and configuration. A large part of the environment configuration can be performed using these tools and for everything else there are always executable blocks, which allow you to call out to PowerShell. However if you do use an executable block, ensure you replace it with the actual package should it start being supported by default. It isn’t clean or easy to manage lots of executable blocks.

And lets not forget our new friend Chocolatey, which will allow you to install system packages. More and more applications are becoming available in Chocolatey so watch this space…

##PowerShell & WMI

For everything else there is PowerShell. Specifically, WindowsFeatures and PowerShell for Windows Server 2008 r2 has a server manager module. This module has commandlets that allow you to add a Windows server feature. So basically with PowerShell and WMI (Windows Management Infrastructure and WinRM (Windows Remoting)) you can pretty much do anything that the Windows Server API will let you, including automating Active Directory commands. The caveat is that your Windows Server needs to be at least Windows Server 2008, when PowerShell support became a built in feature. Before then… good luck!

However it isn’t all sunshine and lollipops. WinRM is actually pretty painful and fiddly to use and as I’ve said PowerShell is an ugly and procedural language. You have to be careful not to let it grow arms and legs and become difficult to understand (and therefore, difficult to maintain). We all know what happens to that kind of code.

##More Pain

There are a few other pain points I would be remiss not to mention while we are at it. Let’s start with registries. In Windows we have 32bit and 64bit registries. Both Puppet and Chef have issues with living on one registry and installing to another. Once you end up in this space, be prepared to debug and perhaps jump through some hoops to get things working.

Other irritating “features” you need to manage are Windows updates and ISO mounting. ISO mounting is still not built into Windows operating systems so you’ll need to download something like Virtual Clone Drive. And finally there is the cost. Even on Azure, Windows environments are more expensive than Linux, but good luck using that as an excuse to port all your software.

I have probably just thoroughly depressed you and made you consider just doing it on Linux. So, let’s talk about the light at the end of the tunnel. These problems can be solved by you and in my opinion you have two options.

--------------------

###1. Manage the problem

Being aware of a problem is the first step to fixing it. Don’t go into Windows automation believing it’s going to be easy and you won’t have to deal with these little niggles. The best defense is an offense. Be prepared, be careful with registries, ensure you manage windows updates so they don’t manage you and use a version of a Windows that supports PowerShell and WinRM. Right now, I recommend Windows Server 2008 and above.

>“…using a GUI is not the kind of easy we are after”

-------

###2. Create the need for change

I’ve said it before but it is really the only way that things will get better. Microsoft and vendors have a responsibility to respond to requirements from their customers. So create those requirements by making them visible at scale. If we all started trying to do DevOps in Windows tomorrow the vendors would respond by trying to make it easier for us. We just need to remind them that using a GUI is not the kind of easy we are after. Push the community and support open source software (OSS). Even Microsoft is supporting OSS now. For example, they have open-sourced MVC and their Entity Framework, demonstrating that a lot of fantastic tooling and innovation can be built and trusted in this environment.

OK I’ll admit those probably aren’t the nice easy solutions you were after, but given the nature of software, I’m sure if I write this again in five years, we won’t have these problems and we’ll have awesome tools for automating the creation of our infrastructure and the deployment of our applications. We’ll be lamenting how much Puppet and Chef suck and how much better our new tools are.

<center>⁂</center>

Right now we have to create the requirement for change and the only way to do that is for everyone to try and automate what they can. Use the best tool there is and petition to have it better.

Automate what you can, and accept what you can’t… for now.
