
---
title: Why is the audio routing abnormal after the Android device joins the channel?
description: Android 设备进入频道后，耳机无声/语音路由不正常
platform: Android
updatedAt: Mon Jul 01 2019 15:14:09 GMT+0800 (CST)
---
# Why is the audio routing abnormal after the Android device joins the channel?
Some Android sample apps provided by Agora maintain a global RtcEngine instance in WorkerThread that keeps alive while the app is running and is destroyed when the app process is destroyed.

The problem of no audio or abnormal audio routing may occur when developers fail to manage WorkerThread appropriately.

In their design, developers tend to operate on WorkerThread to manage the life cycle of the RtcEngine instance, which is quite right for creating the engine and joining a channel. But when they quit the WorkerThread, they do not destroy the RtcEngine instance. This may cause problems, especially when the life cycle of the WorkerThread is not the same as the app process.

By calling `destroy`, the RtcEngine removes all registered system listeners (in this case, PhoneStateListener), some of which may reference to the Looper of the current Thread. If a system listener is not removed when WorkerThread quits, the listener still monitors but the Looper it references to is already invalid, leading to a dead binder error.

Agora recommends using one of the following solutions to solve this problem:

* Maintain one WorkerThreader.
* Call `destroy` to release the RtcEngine instance when exiting the channel.
