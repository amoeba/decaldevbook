---
layout: chapter
title: Setup
permalink: "setup.html"
toc: true
---

# Setup

Getting set up for Decal plugin development is similar to gettting set up for developing application in C# so you may already have some of these components if you've done any work with C#.
In total, we need to install:

- Asheron's Call
- Decal
- Visual Studio
- .NET Framework 2.0

Installation instructions for Asheron's Call can be found elsewhere (such as [GDLE](https://www.gdleac.com/)) and Decal can be found at [decaldev.com](https://www.decaldev.com/).

## Installing Microsoft Visual Studio

There are many ways to compile a Decal plugin but the simplest way is to install a copy of [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/).
When installing, make sure to check the ".NET desktop development" workload (below).

![]({{ site.url }}/assets/images/visual-studio-install-dotnet.png)

At this point, you should be able to run Visual Studio.
Go ahead and close it and continue on to the next section to install .NET Framework 2.0.

## Installing .NET Framework 2.0

Decal plugins target .NET Framework 2.0 which means two things:

1. You'll need to install .NET Framework 2.0 because Windows no longer ships with it pre-installed
2. Decal plugins are limited to using features of .NET that were available in the 2.0 framework and can't use newer features

> aside "Getting Help"
> Bullet point (2) means that you'll need to be careful when browsing the [.NET API documentation](https://docs.microsoft.com/en-us/dotnet/api/) because it defaults to the latest version of the .NET Framework and not 2.0.
> For example, if you search [Google](https://google.com) for [.NET dictionary](https://www.google.com/search?q=.net+dictionary), the first result (as of writing) takes you to <a href="https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2">https://docs.microsoft.com/en-us/dotnet/api/system.collections.generic.dictionary-2</a> which shows the API documentation for the latest version of the .NET Framework.
>
> Be sure to change the framework version to 2.0 to get accurate documentation:
>
> ![screenshot of a Microsoft .NET API showing how to switch to showing the docs for .NET Framework 2.0]({{ site.url }}/assets/images/dotnet-api-select-2.0.png)

To install .NET Framework 2.0:

1. Open your Start menu
2. Open "Turn Windows features on or off"
3. Check ".NET Framework 3.5 (includes .NET 2.0 and 3.0)"

![screenshot of the Windows Features application and checking ".NET Framework 3.5 (includes .NET 2.0 and 3.0)"]({{ site.url}}/assets/images/windows-features-install-dotnet-2.0.png)

4. Click "OK"

You should see a progess bar and be asked to "Let Windows Update download the files for you" . 

At this point, you're all set up to create your first Decal plugin.
Continue on to [Chapter 2]({{ site.url }}/hello_world.html).