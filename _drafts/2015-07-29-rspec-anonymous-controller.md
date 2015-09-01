---
layout: post
title:  "RSPec Anonymous Controller?"
date:   2015-07-26 21:22:49
comments: true
author: Přemysl Donát
---
Most of the times, RSPec tries to infer a lot of things from how you describe your examples. But beware at the `controller` block, which sets anonymous controller fro testing purposes. By default it inherits from ApplicationController, not the controller, you are descibing with describe.

Link to official docu:
[]()
