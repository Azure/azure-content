---
author: karanmish
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-web.md)]

### Record calls locally
> [!NOTE]
> This API is provided as a preview for developers and may change based on feedback that we receive. Do not use this API in a production environment. To use this api please use 'beta' release of Azure Communication Services Calling Web SDK.

Local recording is an extended feature of the core `Call` API and works very similar to the recording feature used for cloud recording. You first need to import calling Features from the Calling SDK:

```js
import { Features} from "@azure/communication-calling";
```

Then you can get the local recording feature API object from the call instance:

```js
const localCallRecordingApi = call.feature(Features.LocalRecording);
```

Then, to check if the call is being recorded locally, inspect the `isLocalRecordingActive` property of `callRecordingApi`. It returns `Boolean`.

```js
const isLocalRecordingActive = localCallRecordingApi.isLocalRecordingActive;
```

You can also get a list of local recordings by using the `recordings` property of `callRecordingApi`. It returns `LocalRecordingInfo[]`.

```js
const recordings = localCallRecordingApi.recordings;

recordings.forEach(r => {
    console.log("MRI: ${r.initiatorIdentifier?.microsoftTeamsUserId}, State: ${r.state});
```

You can subscribe to recording changes:

```js
const isLocalRecordingActiveChangedHandler = () => {
    console.log(localCallRecordingApi.isLocalRecordingActive);
};    

localCallRecordingApi.on('isLocalRecordingActiveChanged', isLocalRecordingActiveChangedHandler);
```

You can also subscribe to 'localRecordingsUpdated' and get a collection of updated recordings. This event is triggered whenever there is a recording update

```js
const localRecordingsUpdateddHandler = (args: { added: SDK.LocalRecordingInfo[], removed: SDK.LocalRecordingInfo[]}) => {
                        console.log('Local recording started by: ');
                        args.added?.forEach(a => {
                            console.log('UserMRI: ${a.initiatorIdentifier?.microsoftTeamsUserId});
                        });

                        console.log('Local recording stopped by: ');
                        args.removed?.forEach(r => {
                            console.log('UserMRI: ${r.initiatorIdentifier?.microsoftTeamsUserId});
                        });
                    };
localCallRecordingApi.on('localRecordingsUpdated', localRecordingsUpdateddHandler);
```
