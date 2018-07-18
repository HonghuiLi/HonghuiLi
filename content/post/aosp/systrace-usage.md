+++
date = 2018-01-18
draft = false
tags = []
title = "Performance tools"
math = true
+++

{{% toc %}}

# [Systrace](https://developer.android.com/studio/command-line/systrace.html)

This report provides an overall picture of an Android device’s system processes for a given period of time. It also inspects the captured tracing information to highlight problems that it observes, such as UI jank while displaying motion or animation, and provides recommendations about how to fix them. However, systrace doesn't collect information about code execution within your app process. For more detailed information about which methods your app is executing and how much CPU resources it's using, use Android Studio's built-in CPU profiler, or generate trace logs and view them using Traceview.

## Command
1. AOSP master:   
  https://android.googlesource.com/platform/external/chromium-trace  
2. Android SDK: ${android-sdk}/platform-tools/systrace/   
   alias systrace-sdk='python ${android-sdk}/platform-tools/systrace/systrace.py'
3. Syntax  
   python systrace.py [options] [categories]  

For example, the following command calls systrace to record device processes over a period of 10 seconds, include graphics processes, and generate a HTML report named mynewtrace:
> python systrace.py --time=10 -o mynewtrace.html gfx  

Global options	Description
> -h | --help	Show the help message.  
> -l | --list-categories	Lists the tracing categories available to your connected device.


## analysis
