+++
date = 2018-01-18
draft = true
tags = []
title = "Performance tools"
math = true
+++

# Android Interfaces and Architecture

Ref: http://source.android.com/devices/index.html
---

## Trace

```shell
adb shell atrace -h

mkdir -p ~/bin && cd ~/bin
git clone https://android.googlesource.com/platform/external/chromium-trace
alias systrace='python ~/bin/chromium-trace/systrace.py'
cd -
```

### Interesting tags

 + TRACE_TAG_INPUT
 + TRACE_TAG_HAL


 + TRACE_TAG_SYSTEM_SERVER
 + TRACE_TAG_WINDOW_MANAGER
 + TRACE_TAG_ACTIVITY_MANAGER


+ TRACE_TAG_VIEW
+ TRACE_TAG_GRAPHICS


### Code

```java
frameworks/base/core/java/android/os/Trace.java

package android.os;

import dalvik.annotation.optimization.FastNative;

/**
 * Writes trace events to the system trace buffer.  These trace events can be
 * collected and visualized using the Systrace tool.
 *
 * <p>This tracing mechanism is independent of the method tracing mechanism
 * offered by {@link Debug#startMethodTracing}.  In particular, it enables
 * tracing of events that occur across multiple processes.
 * <p>For information about using the Systrace tool, read <a
 * href="{@docRoot}tools/debugging/systrace.html">Analyzing Display and Performance
 * with Systrace</a>.
 */
public final class Trace {
    /*
     * Writes trace events to the kernel trace buffer.  These trace events can be
     * collected using the "atrace" program for offline analysis.
     */

    private static final String TAG = "Trace";

    // These tags must be kept in sync with system/core/include/cutils/trace.h.
    // They should also be added to frameworks/native/cmds/atrace/atrace.cpp.
    /** @hide */
    public static final long TRACE_TAG_NEVER = 0;
    /** @hide */
    public static final long TRACE_TAG_ALWAYS = 1L << 0;
    /** @hide */
    public static final long TRACE_TAG_GRAPHICS = 1L << 1;
    /** @hide */
    public static final long TRACE_TAG_INPUT = 1L << 2;
    /** @hide */
    public static final long TRACE_TAG_VIEW = 1L << 3;
    /** @hide */
    public static final long TRACE_TAG_WEBVIEW = 1L << 4;
    /** @hide */
    public static final long TRACE_TAG_WINDOW_MANAGER = 1L << 5;
    /** @hide */
    public static final long TRACE_TAG_ACTIVITY_MANAGER = 1L << 6;
    /** @hide */
    public static final long TRACE_TAG_SYNC_MANAGER = 1L << 7;
    /** @hide */
    public static final long TRACE_TAG_AUDIO = 1L << 8;
    /** @hide */
    public static final long TRACE_TAG_VIDEO = 1L << 9;
    /** @hide */
    public static final long TRACE_TAG_CAMERA = 1L << 10;
    /** @hide */
    public static final long TRACE_TAG_HAL = 1L << 11;
    /** @hide */
    public static final long TRACE_TAG_APP = 1L << 12;
    /** @hide */
    public static final long TRACE_TAG_RESOURCES = 1L << 13;
    /** @hide */
    public static final long TRACE_TAG_DALVIK = 1L << 14;
    /** @hide */
    public static final long TRACE_TAG_RS = 1L << 15;
    /** @hide */
    public static final long TRACE_TAG_BIONIC = 1L << 16;
    /** @hide */
    public static final long TRACE_TAG_POWER = 1L << 17;
    /** @hide */
    public static final long TRACE_TAG_PACKAGE_MANAGER = 1L << 18;
    /** @hide */
    public static final long TRACE_TAG_SYSTEM_SERVER = 1L << 19;
    /** @hide */
    public static final long TRACE_TAG_DATABASE = 1L << 20;
    /** @hide */
    public static final long TRACE_TAG_NETWORK = 1L << 21;
    /** @hide */
    public static final long TRACE_TAG_ADB = 1L << 22;

    private static final long TRACE_TAG_NOT_READY = 1L << 63;
    private static final int MAX_SECTION_NAME_LEN = 127;

    // Must be volatile to avoid word tearing.
    private static volatile long sEnabledTags = TRACE_TAG_NOT_READY;

    private static int sZygoteDebugFlags = 0;

    private static native long nativeGetEnabledTags();
    private static native void nativeSetAppTracingAllowed(boolean allowed);
    private static native void nativeSetTracingEnabled(boolean allowed);

    @FastNative
    private static native void nativeTraceCounter(long tag, String name, int value);
    @FastNative
    private static native void nativeTraceBegin(long tag, String name);
    @FastNative
    private static native void nativeTraceEnd(long tag);
    @FastNative
    private static native void nativeAsyncTraceBegin(long tag, String name, int cookie);
    @FastNative
    private static native void nativeAsyncTraceEnd(long tag, String name, int cookie);
```

``` cpp
system/core/include/cutils/trace.h

/**
 * The ATRACE_TAG macro can be defined before including this header to trace
 * using one of the tags defined below.  It must be defined to one of the
 * following ATRACE_TAG_* macros.  The trace tag is used to filter tracing in
 * userland to avoid some of the runtime cost of tracing when it is not desired.
 *
 * Defining ATRACE_TAG to be ATRACE_TAG_ALWAYS will result in the tracing always
 * being enabled - this should ONLY be done for debug code, as userland tracing
 * has a performance cost even when the trace is not being recorded.  Defining
 * ATRACE_TAG to be ATRACE_TAG_NEVER or leaving ATRACE_TAG undefined will result
 * in the tracing always being disabled.
 *
 * ATRACE_TAG_HAL should be bitwise ORed with the relevant tags for tracing
 * within a hardware module.  For example a camera hardware module would set:
 * #define ATRACE_TAG  (ATRACE_TAG_CAMERA | ATRACE_TAG_HAL)
 *
 * Keep these in sync with frameworks/base/core/java/android/os/Trace.java.
 */
#define ATRACE_TAG_NEVER            0       // This tag is never enabled.
#define ATRACE_TAG_ALWAYS           (1<<0)  // This tag is always enabled.
#define ATRACE_TAG_GRAPHICS         (1<<1)
#define ATRACE_TAG_INPUT            (1<<2)
#define ATRACE_TAG_VIEW             (1<<3)
#define ATRACE_TAG_WEBVIEW          (1<<4)
#define ATRACE_TAG_WINDOW_MANAGER   (1<<5)
#define ATRACE_TAG_ACTIVITY_MANAGER (1<<6)
#define ATRACE_TAG_SYNC_MANAGER     (1<<7)
#define ATRACE_TAG_AUDIO            (1<<8)
#define ATRACE_TAG_VIDEO            (1<<9)
#define ATRACE_TAG_CAMERA           (1<<10)
#define ATRACE_TAG_HAL              (1<<11)
#define ATRACE_TAG_APP              (1<<12)
#define ATRACE_TAG_RESOURCES        (1<<13)
#define ATRACE_TAG_DALVIK           (1<<14)
#define ATRACE_TAG_RS               (1<<15)
#define ATRACE_TAG_BIONIC           (1<<16)
#define ATRACE_TAG_POWER            (1<<17)
#define ATRACE_TAG_PACKAGE_MANAGER  (1<<18)
#define ATRACE_TAG_SYSTEM_SERVER    (1<<19)
#define ATRACE_TAG_DATABASE         (1<<20)
#define ATRACE_TAG_NETWORK          (1<<21)
#define ATRACE_TAG_ADB              (1<<22)
#define ATRACE_TAG_LAST             ATRACE_TAG_ADB

// Reserved for initialization.
#define ATRACE_TAG_NOT_READY        (1ULL<<63)

#define ATRACE_TAG_VALID_MASK ((ATRACE_TAG_LAST - 1) | ATRACE_TAG_LAST)
```

# Input Technical Information


## InputFlinger


## InputManagerService


## WindowManagerService

com.android.server.SystemServer#startOtherServices

``` java
traceBeginAndSlog("StartInputManagerService");
inputManager = new InputManagerService(context);
Trace.traceEnd(Trace.TRACE_TAG_SYSTEM_SERVER);

traceBeginAndSlog("StartWindowManagerService");
wm = WindowManagerService.main(context, inputManager,
        mFactoryTestMode != FactoryTest.FACTORY_TEST_LOW_LEVEL,
        !mFirstBoot, mOnlyCore);
ServiceManager.addService(Context.WINDOW_SERVICE, wm);
ServiceManager.addService(Context.INPUT_SERVICE, inputManager);
Trace.traceEnd(Trace.TRACE_TAG_SYSTEM_SERVER);

traceBeginAndSlog("StartVrManagerService");
mSystemServiceManager.startService(VrManagerService.class);
Trace.traceEnd(Trace.TRACE_TAG_SYSTEM_SERVER);

mActivityManagerService.setWindowManager(wm);

inputManager.setWindowManagerCallbacks(wm.getInputMonitor());
inputManager.start();

```


# JobScheduler
adb shel dumpsys jobscheduler

kernel
cpuset, SMP
GPU ?

# Toolchains
