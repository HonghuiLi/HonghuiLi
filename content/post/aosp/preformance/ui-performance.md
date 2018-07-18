+++
date = 2018-01-18
draft = true
tags = []
title = "Performance tools"
math = true
+++

# [评估性能](https://source.android.com/devices/tech/debug/eval_perf)

## caption & jitter


### 与抖动相关的卡顿
应用线程调度程序延迟

  线程运行时间过长
  使用温控调频中描述的 cpuset。
  增加 CONFIG_HZ 的值。

  使用 sys.use_fifo_ui
  您可以尝试将 sys.use_fifo_ui 属性设置为 1，从而将界面线程处于就绪状态的时间降至零。
  除非您有容量感知型 RT 调度程序，否则不能在异构 CPU 配置上使用此选项。而且，目前市面上的 RT 调度程序均不具备容量感知功能。我们正在努力针对 EAS 开发这种类型的调度程序，但尚未推出。默认的 RT 调度程序完全基于两个因素：RT 优先级，以及 CPU 是否已经具有相同或更高优先级的 RT 线程。




# [Testing UI Performance](https://developer.android.com/training/testing/performance.html)
> adb shell dumpsys gfxinfo

Analyzing with Profile GPU Rendering
https://developer.android.com/topic/performance/rendering/profile-gpu.html


# [Automating UI Performance Tests](https://codelabs.developers.google.com/codelabs/android-perf-testing/index.html?index=..%2F..%2Findex#0)


# hwui JankTracker
frameworks/base/libs/hwui/JankTracker.cpp
