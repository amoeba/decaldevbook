---
layout: chapter
title: Hello Auberean
permalink: "hello_world.html"
toc: true
---

# Hello Auberean Plugin

TODO

## Create a new project

TODO

![]({{ site.url }}/assets/images/visual-studio-new-project-01-menu.png)

1. Select "Class Library (.NET Framework)
2. Give your new project a name, such as "Hello Auberean" and save it somewhere you'll be able to find it later
3. Make sure to select .NET Framework 2.0 under the Framework option. You can change this later but it's easy enough to do now
4. Click  OK

![]({{ site.url }}/assets/images/visual-studio-new-project-02-choose.png)


## PluginCore.cs

TODO

```c#
using System;
using System.Collections.Generic;
using System.Text;

using Decal.Adapter;

namespace HelloAuberean
{
    public class PluginCore : PluginBase
    {
        protected override void Startup()
        {
            Core.CharacterFilter.LoginComplete += CharacterFilter_LoginComplete;
        }

        protected override void Shutdown()
        {
            Core.CharacterFilter.LoginComplete -= CharacterFilter_LoginComplete;
        }
        
        private void CharacterFilter_LoginComplete(object sender, EventArgs e)
        {
            Host.Actions.AddChatText("Hello, Auberean!", 1);
        }
    }
}
```
## Compiling

TODO

![]({{ site.url }}/assets/images/visual-studio-6-build.png)
