---
layout: chapter
title: Debugging
permalink: "debugging.html"
toc: true
---

# Debugging

At some point during plugin development, you're eventually going to need to debug your plugin.
Either while building new features of fixing bugs in existing ones.
Generally speaking, debugging Decal plugins is a little harder than with traditional programs because we can't attach a debugger directly to the plugin to do things like inspect local variables or pause execution.

If you've already implemented logging of some form, you might prefer to debug your plugin by writing to your logs or, in a pinch, even writing to chat ingame.
I've done this for years and it has served me fairly well.
The upside of this approach is that you're probably familiar with how to work with these systems but there are downsides:

- Writing to chat (e.g., `Host.Actions.AddChatText`): You can't always write to chat. Either some parts of your plugin starts before ingame chat is available or some parts of your code aren't on the main plugin thread and therefore cannot write to chat.
- Writing to your logs: How do you debug your logging implementation? If you want to use your logs for always-on tracing and also for debugging, you'll probably end up implementing log levels and more.

Ultimately, you'll need to find a debugging solution that fits your needs and writing to chat or a log on disk may work perfectly well for you.
However, I think there's one other solution that is at sufficiently better than these other two: [DebugView](https://docs.microsoft.com/en-us/sysinternals/downloads/debugview).

## Sysinternals' DebugView

[DebugView](https://docs.microsoft.com/en-us/sysinternals/downloads/debugview) is a program from [Sysinternals](https://en.wikipedia.org/wiki/Sysinternals) which I think makes for a better debugging experience. Sysinternals used to be separate from [Microsoft](https://www.microsoft.com) but was [acquired](https://web.archive.org/web/20070314051129/http://www.winternals.com/Company/PressRelease92.aspx) in 2006 and you might find their [other tools](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) useful as well.

With DebugView, you add calls to `System.Diagnostics.Debug.WriteLine` into your plugin code and they'll show up in DebugView. To boot, when you compile your plugin for Release, these calls will all get removed during compilation so your plugin users won't see any performance hit so you can leave them in your plugin code without worry.

Here's a simple example where we add a `Debug` call to the `Startup` method of our plugin's `PluginCore`:

```csharp
using System;
using System.Diagnostics; // <-- Added for Debug.WriteLine()
using Decal.Adapter;
using Decal.Adapter.Wrappers;

namespace TestPlugin
{
    [FriendlyName("TestPlugin")]
    public class PluginCore : PluginBase
    {
        protected override void Startup()
        {
            try
            {
                Debug.WriteLine("[TestPlugin] PluginCore::Startup()");
                ...
```

With this, when DebugView is launched _before_ you launch the game, you should see messages such as:

![]({{ site.url }}/assets/images/DebugView.png)

I encourage you to try out this approach and see if it works well for you.
