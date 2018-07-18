+++
date = 2018-01-18
draft = true
tags = []
title = "touch feature"
math = true
+++

# 窄边框手机 手掌根部 防误触优化

### 初步方案

1. 屏蔽实现初步方案：
>1. InputEventReceiver.dispatchInputEvent 全局屏蔽预处理
>2. 针对Window DcoreView 可选各个场景屏蔽层处理： android.view.ViewRootImpl$WindowInputEventReceiver.onInputEvent
去掉场景：WindowManager WindowDeatrh,PhoneWindowManager Navigation Bar
>3. InputEvent延时 N ms, 检查是否有多点触摸，评估是误触的概率.
at android.view.ViewRootImpl.enqueueInputEvent(ViewRootImpl.java:6220)

```java
Stack:
at android.view.ViewRootImpl.enqueueInputEvent(ViewRootImpl.java:6220)
at android.view.ViewRootImpl$WindowInputEventReceiver.onInputEvent(ViewRootImpl.java:6388)
at android.view.InputEventReceiver.dispatchInputEvent(InputEventReceiver.java:187)
at android.os.MessageQueue.nativePollOnce(MessageQueue.java:-1)
```

[ViewRootImpl](http://jira.n.xiaomi.com/secure/attachment/542233/542233_ViewRootImpl.png!)

2. 屏蔽参数设计
(1).目前没有用户数据，预设屏蔽区域：左右边缘，左右下角
(2).触摸点是否在屏蔽区域判断：
[Mask.png](http://jira.n.xiaomi.com/secure/attachment/542240/542240_Mask.png)
(3). Window DecorView屏蔽区域设计，ContentView 改变时加载屏蔽区域.
(4). 后续环形屏蔽层处理.

3.不同机型，不用用户，历史热点统计
(1).记录白名单DecorView的Window, 触摸点位置计数
(2).统计记录：DroppBox, Mqsas 云控读取
(2).热点显示：Web UI控件，Mathmatic可视化分析 网络部署
4. 历史数据反推误触概率：维度，算法，历史数据学习, 效果反馈，具体效果有待调查
算法:贝叶斯推断 !Bayes' theorem.png!，历史数据还需要参考下: tensorflow

实现：

实现屏蔽层:
1. 目前没有用户数据，预设屏蔽区域：左右边缘，左右下角
2. 屏蔽层设置界面，记录触摸点int坐标，
3. 屏蔽层转换为Json文本
4. 屏蔽层从云控读取配置，手机端可以不考虑多用户配置，存储到SettingPreference.
5. 屏蔽层边界不需要Bezier平滑，以触摸点数据为控制点的贝塞尔曲线，各触摸点都在曲线上，
InputReader有插值过滤，已经平滑过触摸轨迹.
```java
// Mathmatic:
hex[s_String] := FromDigits[s, 10];
data = ReadList["~/download/test.log", {hex[Word], hex[Word]}];
Graphics[{BezierCurve[data], Green, Line[data], Red, Point[data]}]
{code}
```
6. 触摸点是否在屏蔽区域判断
