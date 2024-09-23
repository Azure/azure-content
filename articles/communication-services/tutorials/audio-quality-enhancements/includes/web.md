---
title: Tutorial - Add audio noise suppression ability to your Web calls 
titleSuffix: An Azure Communication Services tutorial on how to enable advanced noise suppression
description: Learn how to add audio effects in your calls using Azure Communication Services.
author: sloanster
ms.author: micahvivion

services: azure-communication-services
ms.date: 09/11/2024
ms.topic: include
ms.service: azure-communication-services
ms.subservice: calling
---

The Azure Communication Services audio effects **noise suppression** abilities can improve your audio calls by filtering out unwanted background noises. **Noise suppression** is a technology that removes background noises from audio calls. It makes audio calls clearer and better by eliminating background noise, making it easier to talk and listen. Noise suppression can also reduce distractions and tiredness caused by noisy places. For example, if you're taking an Azure Communication Services WebJS call in a coffee shop with considerable noise, turning on noise suppression can make the call experience better.

## Using audio effects - install the calling effects npm package
> [!IMPORTANT]
> This tutorial employs the Azure Communication Services Calling SDK version `1.28.4` or later, alongside the Azure Communication Services Calling Effects SDK version `1.1.1` or newer. The GA (stable) version **`1.28.4`** and above of the calling SDK support noise suppression features. Alternatively, if you opt to use the public preview version, calling SDK versions `1.24.2-beta.1` and higher also support noise suppression.
> 
> Current browser support for adding audio noise suppression effects is only available on Chrome and Edge Desktop browsers.

> [!IMPORTANT]
> The calling effects library cannot be used standalone and can only work when used with the Azure Communication Calling client library for WebJS.

Use the `npm install` command to install the Azure Communication Services Audio Effects SDK for JavaScript.

> [!NOTE]
> If you are using the GA version of the calling SDK you must use the [GA version](https://www.npmjs.com/package/@azure/communication-calling-effects/v/1.1.1) of the effects SDK.
```console
@azure/communication-calling-effects@1.1.1
```

> [!NOTE]
> If you are using the public preview of the calling SDK you must use the [beta version](https://www.npmjs.com/package/@azure/communication-calling-effects/v/1.1.1-beta.2) of the effects SDK.
```console
@azure/communication-calling-effects@1.1.1-beta
```
## Loading the Noise Suppression effects library

For details on the interface detailing audio effects properties and methods, consult the [Audio Effects Feature interface](/javascript/api/azure-communication-services/@azure/communication-calling/audioeffectsfeature?view=azure-communication-services-js&preserve-view=true) API documentation page.

To use `noise suppression` audio effects within the Azure Communication Calling SDK, you need the `LocalAudioStream` that is currently in the call. You need access to the `AudioEffects` API of the `LocalAudioStream` to start and stop audio effects.
```js
import * as AzureCommunicationCallingSDK from '@azure/communication-calling'; 
import { DeepNoiseSuppressionEffect } from '@azure/communication-calling-effects'; 

// Get the LocalAudioStream from the localAudioStream collection on the call object
// 'call' here represents the call object.
const localAudioStreamInCall = call.localAudioStreams[0];

// Get the audio effects feature API from LocalAudioStream
const audioEffectsFeatureApi = localAudioStreamInCall.feature(AzureCommunicationCallingSDK.Features.AudioEffects);

// Subscribe to useful events that show audio effects status
audioEffectsFeatureApi.on('effectsStarted', (activeEffects: ActiveAudioEffects) => {
    console.log(`Current status audio effects: ${activeEffects}`);
});


audioEffectsFeatureApi.on('effectsStopped', (activeEffects: ActiveAudioEffects) => {
    console.log(`Current status audio effects: ${activeEffects}`);
});


audioEffectsFeatureApi.on('effectsError', (error: AudioEffectErrorPayload) => {
    console.log(`Error with audio effects: ${error.message}`);
});
```
## Checking what audio effects are active
At anytime if you want to check what **noise suppression** effects are currently active, you can use the `activeEffects` property.
The `activeEffects` property returns an object with the names of the current active effects.
```js
// Using the audio effects feature api
const currentActiveEffects = audioEffectsFeatureApi.activeEffects;

// Create the noise suppression instance 
const deepNoiseSuppression = new DeepNoiseSuppressionEffect();
// Its recommened to check support for the effect in the current environment using the isSupported API 
// method. Remember that Noise Supression is only supported on Desktop Browsers for Chrome and Edge

const isDeepNoiseSuppressionSupported = await audioEffectsFeatureApi.isSupported(deepNoiseSuppression);
if (isDeepNoiseSuppressionSupported) {
    console.log('Noise supression is supported in local browser environment');
}

// To start ACS Deep Noise Suppression
await audioEffectsFeatureApi.startEffects({
    noiseSuppression: deepNoiseSuppression
});

// To stop ACS Deep Noise Suppression
await audioEffectsFeatureApi.stopEffects({
    noiseSuppression: true
});

```

## Start a call with Noise Suppression automatically enabled
To start a call with **noise suppression** turned on, you can create a new `LocalAudioStream` with a `AudioDeviceInfo` (the LocalAudioStream source <u>shouldn't</u> be a raw `MediaStream` to use audio effects), and pass it in the `CallStartOptions.audioOptions`:
```js
// As an example, here we are simply creating a LocalAudioStream using the current selected mic on the DeviceManager
const audioDevice = deviceManager.selectedMicrophone;
const localAudioStreamWithEffects = new AzureCommunicationCallingSDK.LocalAudioStream(audioDevice);
const audioEffectsFeatureApi = localAudioStreamWithEffects.feature(AzureCommunicationCallingSDK.Features.AudioEffects);

// Start effect
await audioEffectsFeatureApi.startEffects({
    noiseSuppression: deepNoiseSuppression
});

// Pass the LocalAudioStream in audioOptions in call start/accept options.
await call.startCall({
    audioOptions: {
        muted: false,
        localAudioStreams: [localAudioStreamWithEffects]
    }
});
```

## Turn on Noise Suppression during an ongoing call
There are situations where a user might start a call and not have **noise suppression** turned on, but their current environment might get noisy resulting in them needing to turn on **noise suppression**. To turn on **noise suppression**, you can use the `audioEffectsFeatureApi.startEffects` API.
```js
// Create the noise supression instance 
const deepNoiseSuppression = new DeepNoiseSuppressionEffect();

// Get the LocalAudioStream from the localAudioStream collection on the call object
// 'call' here represents the call object.
const localAudioStreamInCall = call.localAudioStreams[0];

// Get the audio effects feature API from LocalAudioStream
const audioEffectsFeatureApi = localAudioStreamInCall.feature(AzureCommunicationCallingSDK.Features.AudioEffects);

// Its recommened to check support for the effect in the current environment using the isSupported method on the feature API. Remember that Noise Supression is only supported on Desktop Browsers for Chrome and Edge
const isDeepNoiseSuppressionSupported = await audioEffectsFeatureApi.isSupported(deepNoiseSuppression);
if (isDeepNoiseSuppressionSupported) {
    console.log('Noise supression is supported in the current browser environment');
}

// To start ACS Deep Noise Suppression,
await audioEffectsFeatureApi.startEffects({
    noiseSuppression: deepNoiseSuppression
});

// To stop ACS Deep Noise Suppression
await audioEffectsFeatureApi.stopEffects({
    noiseSuppression: true
});
```
## More Details
See [Audio Effects Feature interface](/javascript/api/azure-communication-services/@azure/communication-calling/audioeffectsfeature?view=azure-communication-services-js&preserve-view=true) documentation page for extended API feature details.
