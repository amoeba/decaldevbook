---
layout: chapter
title: Debugging
permalink: "debugging.html"
toc: true
---

# Debugging

At some point during plugin development, you're eventually going to need to debug your plugin, either while building new features of fixing bugs in existing ones.
Generally speaking, debugging Decal plugins is a little harder than debugging traditional programs because, when we attach our debugger, breakpoints pause the entire client (`acclient.exe`) until we resume and the server may disconnect us if we're don't resume soon enough.
This is because our Decal plugins live in the client's memory address space and not their own (or Decal's).

There are four main ways to debug your plugin:

1. Writing to ingame chat
2. Writing to a file
3. Using `Debug.WriteLine`
4. Attach a debugger to the client and using breakpoints

None of these methods are perfect and none are the _only_ method I recommend.
Downsides include:

- **Writing to ingame chat (e.g., `Host.Actions.AddChatText`)**: You can't always write to chat. Either some parts of your plugin starts before ingame chat is available or some parts of your code aren't on the main plugin thread and therefore cannot write to chat.
- **Writing to a file**: How do you debug your logging implementation? If you want to use your logs for always-on tracing and also for debugging, you'll probably end up implementing log levels and more.
- **Attach a debugger to the client**: Breakpoints pause the entire client and can cause the server to disconnect your client.

Ultimately, you'll need to find a debugging solution that fits your needs and writing to chat or a log on disk may work perfectly well for you.

## Writing to ingame chat

The immediate feedback of debugging with ingame chat makes this a great option in many scenarios.
The major downside is that you can't always write to chat, depending on which thread you trigger a write from.

Writing information to ingame chat for logging purposes works just like normal:

```csharp
Host.Actions.AddChatText("My debug info...", 1);
```

## Writing to a file

Writing information to a file is, hands down, the best way to help your users usefully reports bugs to you.
The idea here is that we continuously write out information about our plugin's state and exceptions to files so, when a user reports an issue, we can simply ask them for their log files.

This example logging clase is based off of [Virindi's](http://www.virindi.net/repos/virindi_public/trunk/VirindiReporter/VirindiReporter/errorLogging.cs
):

```csharp
class Logging
{
    static void LogMessage(string path, string message)
    {
        System.IO.StreamWriter sw = new System.IO.StreamWriter(path, true);

        sw.Write(DateTime.Now.ToString());
        sw.Write(": );
        sw.Write(message);

        sw.Close();
    }

    static void LogError(string path, Exception ex)
    {
        System.IO.StreamWriter sw = new System.IO.StreamWriter(path, true);

        sw.WriteLine("============================================================================");
        sw.WriteLine(DateTime.Now.ToString());
        sw.WriteLine("Error: " + ex.Message);
        sw.WriteLine("Source: " + ex.Source);
        sw.WriteLine("Stack: " + ex.StackTrace);
        if (ex.InnerException != null)
        {
            sw.WriteLine("Inner: " + ex.InnerException.Message);
            sw.WriteLine("Inner Stack: " + ex.InnerException.StackTrace);
        }
        sw.WriteLine("============================================================================");
        sw.WriteLine("");

        sw.Close();
    }
}
```

One important caveat to writing to a file is to be sure that the path you're writing to is unique per client.
Even if you are appending to the file, multiple clients will contend to perform a write and some will encounter exceptions.
The best way to make sure this doesn't happen is to key logfile paths off of the server _and_ character name.

## Sysinternals' DebugView

[DebugView](https://docs.microsoft.com/en-us/sysinternals/downloads/debugview) is a program from [Sysinternals](https://en.wikipedia.org/wiki/Sysinternals) which I think makes for a better debugging experience than writing to ingame chat.
Sysinternals used to be separate from [Microsoft](https://www.microsoft.com) but was [acquired](https://web.archive.org/web/20070314051129/http://www.winternals.com/Company/PressRelease92.aspx) in 2006 and you might find their [other tools](https://docs.microsoft.com/en-us/sysinternals/downloads/sysinternals-suite) useful as well.

With DebugView, you add calls to `System.Diagnostics.Debug.WriteLine` into your plugin code and they'll show up when you run DebugView. To boot, when you compile your plugin for Release, these calls will all get removed during compilation so your plugin users won't see any performance hit so you can leave them in your plugin code without worry.

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

## Attaching a Debugger

Attaching a debugger to debug your plugin is by far the most feature-packed way to debug.
The biggest and most obvious downside is that any breakpoints you set in your code will pause the entire client.
If you don't resume from your breakpoint soon enough, your client will be disconnected by the server.

Once your plugin is added to Decal and you've started a client, select "Attach to Process..." from the Debug menu:

![]({{ site.url }}/assets/images/attach-to-process.png)

In the window the opens, find or filter to `acclient.exe`.
You may have to check "Show processes from all users" for `acclient.exe` to show up.

![]({{ site.url }}/assets/images/attach-to-process-dialog.png)

Once attached, you can add breakpoints to any part of your plugin code.

## Summary

No single debugging method will handle all of our debugging needs and, in my experience, a combination of all of the above may be needed.
I strongly encourage you to set up file-based logging and to pre-emptively sprinkle in debugging statements into the more problematic or failure-prone parts of your codebase.
