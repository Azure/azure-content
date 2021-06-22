---
title: Azure Communication Services - best practices
description: Learn more about Azure Communication Services
author: srahaman
manager: akania
services: azure-communication-services

ms.author: srahaman
ms.date: 06/18/2021
ms.topic: bestpractices
ms.service: azure-communication-services
---

# Best practices: Azure Communication Services Calling SDKs
This article provides information about best practices related to the Azure Communication Services Calling SDKs.

## JavaScript SDK
This section provides information about best practices associated with the Azure Communication Services JavaScript voice and video calling SDKs.

### Plug-in Microphone or Enable Microphone From Device Manager When ACS Call in Progress
When there is no Microphone available and later plug-in Microphone or enable Microphone from device manager, then UFD NO_MICROPHONE_DEVICES_ENUMERATED will be raise. on NO_MICROPHONE_DEVICES_ENUMERATED event ask for device permission.

### Stop Video on Page Hide
When user move away from the browser tab, then stop the video given that the video was on.
```JavaScript
document.addEventListener("visibilitychange", function() {
	if (document.visibilityState === 'visible') {
		// TODO: Start Video if it was stopped on visibility change
	} else {
		// TODO: Stop Video if it's on
	}
});
```

### Dispose Video Stream Renderer View
Application should dispose VideoStreamRendererView, or it's parent VideoStreamRenderer instance, if it doesn't need it anymore to render video and it decides to detach if from the DOM

### Hangup the Call on onbeforeunload Event
App should invoke call.hangup on onbeforeunload event

### Hangup the Call on microphoneMuteUnexpectedly User Facing Diagnostic (UFD)
Whey user is on ACS call from iOS Safari and receives the PSTN call then ACS lost the microphone access. To avoid this issue, it's recommended to hangup the call when microphoneMuteUnexpectedly UFD raised.

