---
title: Quickstart - Add video effects to your video calls (Web)
titleSuffix: An Azure Communication Services quickstart
description: Learn how to add video effects in your video calls using Azure Communication Services.
author: sloanster

ms.author: micahvivion
ms.date: 01/09/2023
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
ms.custom: mode-other
---

You can use the Video effects feature to add effects to your video in video calls.

>[!IMPORTANT]
> The Calling Video effects are available starting on the public preview version [1.9.1-beta.1](https://www.npmjs.com/package/@azure/communication-calling/v/1.9.1-beta.1) of the Calling SDK. Please ensure that you use this or a newer SDK when using video effects.

> [!NOTE]
> This API is provided as a preview ('beta') for developers and may change based on feedback that we receive.

> [!NOTE]
> The calling effect library cannot be used standalone and can only work when used with the Azure Communication Calling client library for WebJS (https://www.npmjs.com/package/@azure/communication-calling). 

## Prerequisites
- Setup Azure Communication Services to be able to start video calls. [Follow this guide](../video-calling/video-calling-javascript.md). You'll need the `LocalVideoStream` to be able to start effects.

## Using video effects
### Install the package
Use the `npm install` command to install the Azure Communication Services Effects SDK for JavaScript.
> [!IMPORTANT]
> This quickstart uses the Azure Communication Services Calling SDK version greater than `1.10.0` and the Azure Communication Services Calling Effects SDK version greater than `1.0.0`.
```console
npm install @azure/communication-calling-effects --save
```

> [!NOTE]
> Currently there are two available video effects:
> - Background blur
> - Background replacement with an image
>

To use video effects with the Azure Communication Calling SDK, once you've created a `LocalVideoStream`, you need to get the `VideoEffects` feature API of the `LocalVideoStream` to start/stop video effects:
```js
import * as AzureCommunicationCallingSDK from '@azure/communication-calling'; 

import { BackgroundBlurEffect, BackgroundReplacementEffect } from '@azure/communication-calling-effects'; 

// Get the video effects feature api on the LocalVideoStream 
// (here, localVideoStream is the LocalVideoStream object you created while setting up video calling)
const videoEffectsFeatureApi = localVideoStream.features(AzureCommunicationCallingSDK.Features.VideoEffects); 


// Subscribe to useful events 
videoEffectsFeatureApi.on(‘effectsStarted’, () => { 
    // Effects started
});

videoEffectsFeatureApi.on(‘effectsStopped’, () => { 
    // Effects stopped
}); 

videoEffectsFeatureApi.on(‘effectsError’, (error) => { 
    // Effects error
});
```

### Background blur:
```js
// Create the effect instance 
const backgroundBlurEffect = new BackgroundBlurEffect(); 

// Recommended: Check support 
const backgroundBlurSupported = await backgroundBlurEffect.isSupported(); 

if (backgroundBlurSupported) { 
    // Use the video effects feature api we created to start effects
    await videoEffectsFeatureApi.startEffects(backgroundBlurEffect); 
}
```

### Background replacement with an image:
You need to provide the URL of the image you want as the background to this effect.
> [!IMPORTANT]
> The `startEffects` method will fail if the URL is not of an image or is unreachable/unreadable.
>

> [!NOTE]
> Current supported image formats are: png, jpg, jpeg, tiff, bmp.
>

```js
const backgroundImage = 'https://linkToImageFile'; 

// Create the effect instance 
const backgroundReplacementEffect = new BackgroundReplacementEffect({ 
    backgroundImageUrl: backgroundImage
}); 

// Recommended: Check support
const backgroundReplacementSupported = await backgroundReplacementEffect.isSupported(); 

if (backgroundReplacementSupported) { 
    // Use the video effects feature api as before to start/stop effects 
    await videoEffectsFeatureApi.startEffects(backgroundReplacementEffect); 
}
```

Changing the image for this effect can be done by passing it via the configure method:
```js
const newBackgroundImage = 'https://linkToNewImageFile'; 

await backgroundReplacementEffect.configure({ 
    backgroundImageUrl: newBackgroundImage
});
```

Switching effects can be done using the same method on the video effects feature api:
```js
// Switch to background blur 
await videoEffectsFeatureApi.startEffects(backgroundBlurEffect); 

// Switch to background replacement 
await videoEffectsFeatureApi.startEffects(backgroundReplacementEffect);
```

To stop effects:
```js
await videoEffectsFeatureApi.stopEffects();
```
