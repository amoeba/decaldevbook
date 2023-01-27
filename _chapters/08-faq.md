---
layout: chapter
title: FAQ
permalink: "faq.html"
toc: true
---

## Build Error: cannot be used across assembly boundaries because it has a generic type argument that is an embedded interop type

If you get errors while building your plugin like below:

```
'GenericDisposableWrapper<World>' from assembly 'Decal.Adapter, Version=2.9.7.5, Culture=neutral, PublicKeyToken=bd1c8ce002ce221e' cannot be used across assembly boundaries because it has a generic type argument that is an embedded interop type
```

Make sure all of your assembly references have "Embed Interop Types" set to "False" by going to the Properties pane in Visual Studio for each and setting the value appropriately.
