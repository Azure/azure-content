---
title: Quickstart - Join a Teams meeting
author: askaur
ms.author: askaur
ms.date: 02/17/2020
ms.topic: quickstart
ms.service: azure-communication-services
---

#### Install the chat packages

Use the `npm install` command to install the below Communication Services chat client libraries for JavaScript.

```console
npm install @azure/communication-signaling --save

npm install @azure/communication-chat --save
```

The `--save` option lists the library as a dependency in your **package.json** file.

## Add the Teams UI controls

Replace code in index.html with following snippet.
The text boxes at the top of the page will be used to enter the Teams meeting context and meeting thread id, and the 'Join Teams Meeting' button will be used to join the specified meeting.
A chat pop-up will appear at the bottom of the page. It can be use to send messages on the meeting thread, and it will display in real time any messages sent on the thread while the ACS user is a member.

```html
<!DOCTYPE html>
<html>
   <head>
      <title>Communication Client - Calling and Chat Sample</title>
      <style>
         body {box-sizing: border-box;}
         /* The popup chat - hidden by default */
         .chat-popup {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #f1f1f1;
         z-index: 9;
         }
         .message-box {
         display: none;
         position: fixed;
         bottom: 0;
         left: 15px;
         border: 3px solid #FFFACD;
         z-index: 9;
         }
         .form-container {
         max-width: 300px;
         padding: 10px;
         background-color: white;
         }
         .form-container textarea {
         width: 90%;
         padding: 15px;
         margin: 5px 0 22px 0;
         border: none;
         background: #e1e1e1;
         resize: none;
         min-height: 50px;
         }
         .form-container .btn {
         background-color: #4CAF40;
         color: white;
         padding: 14px 18px;
         margin-bottom:10px;
         opacity: 0.6;
         border: none;
         cursor: pointer;
         width: 100%;
         }
         .container {
         border: 1px solid #dedede;
         background-color: #F1F1F1;
         border-radius: 3px;
         padding: 8px;
         margin: 8px 0;
         }
         .darker {
         border-color: #ccc;
         background-color: #ffdab9;
         margin-left: 25px;
         margin-right: 3px;
         }
         .lighter {
         margin-right: 20px;
         margin-left: 3px;
         }
         .container::after {
         content: "";
         clear: both;
         display: table;
         }
      </style>
   </head>
   <body>
      <h4>Azure Communication Services</h4>
      <h1>Calling and Chat Quickstart</h1>
          <input id="teams-link-input" type="text" placeholder="Teams meeting link"
        style="margin-bottom:1em; width: 300px;" />
          <input id="thread-id-input" type="text" placeholder="Chat thread id"
        style="margin-bottom:1em; width: 300px;" />
        <p>Call state <span style="font-weight: bold" id="call-state">-</span></p>
      <div>
        <button id="join-meeting-button" type="button">
            Join Teams Meeting
        </button>
        <button id="hang-up-button" type="button" disabled="true">
            Hang Up
        </button>
      </div>
      <div class="chat-popup" id="chat-box">
         <div id="messages-container"></div>
         <form class="form-container">
            <textarea placeholder="Type message.." name="msg" id="message-box" required></textarea>
            <button type="button" class="btn" id="send-message">Send</button>
         </form>
      </div>
      <script src="./bundle.js"></script>
   </body>
</html>
```

## Enable the Teams UI controls

Replace the content of client.js file with the following snippet.

```javascript
import { CallClient, CallAgent } from "@azure/communication-calling";
import { AzureCommunicationUserCredential } from "@azure/communication-common";
import { CommunicationIdentityClient } from "@azure/communication-administration";
import { ChatClient } from "@azure/communication-chat";

let call;
let callAgent;
let chatClient;
let chatThreadClient;

const meetingLinkInput = document.getElementById('teams-link-input');
const threadIdInput = document.getElementById('thread-id-input');
const callButton = document.getElementById("join-meeting-button");
const hangUpButton = document.getElementById("hang-up-button");
const callStateElement = document.getElementById('call-state');

const messagesContainer = document.getElementById("messages-container");
const chatBox = document.getElementById("chat-box");
const sendMessageButton = document.getElementById("send-message");
const messagebox = document.getElementById("message-box");

var messages = '<div class="container lighter">Welcome to the meeting, ACS user!</div>';

async function init() {
  const connectionString = "<ACS CONNECTION STRING>";
  const endpointUrl = "<ACS ENDPOINT URL>";

  const identityClient = new CommunicationIdentityClient(connectionString);

  let identityResponse = await identityClient.createUser();
  console.log(
    `\nCreated an identity with ID: ${identityResponse.communicationUserId}`
  );

  let tokenResponse = await identityClient.issueToken(identityResponse, [
    "voip",
    "chat",
  ]);
  const { token, expiresOn } = tokenResponse;
  console.log(
    `\nIssued an access token that expires at ${expiresOn}:`
  );
  console.log(token);

  const callClient = new CallClient();
  const tokenCredential = new AzureCommunicationUserCredential(token);
  callAgent = await callClient.createCallAgent(tokenCredential);
  callButton.disabled = false;

  chatClient = new ChatClient(
    endpointUrl,
    new AzureCommunicationUserCredential(token)
  );

  console.log('Azure Communication Chat client created!');

}

init();

callButton.addEventListener("click", async () => {
  // join with meeting link
  call = callAgent.join({meetingLink: meetingLinkInput.value}, {});
    
  call.on('callStateChanged', () => {
        callStateElement.innerText = call.state;
  })
  // toggle button and chat box states
  chatBox.style.display = "block";
  hangUpButton.disabled = false;
  callButton.disabled = true;

  messagesContainer.innerHTML = messages;
  
  console.log(call);

  // open notifications channel
  await chatClient.startRealtimeNotifications();

  // subscribe to new notification
  chatClient.on("chatMessageReceived", (e) => {
    console.log("Notification chatMessageReceived!");
    renderMessage(e.content);
  });
  chatThreadClient = await chatClient.getChatThreadClient(threadIdInput.value);
});

async function renderMessage(message) {
   messages += '<div class="container lighter">' + message + '</div>';
}

hangUpButton.addEventListener("click", async () => 
  {
    // end the current call
    await call.hangUp({ forEveryone: true });

    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    callStateElement.innerText = '-';

    // toggle chat states
    chatBox.style.display = "none";
    messages = "";
  });


sendMessageButton.addEventListener("click", async () =>
  {
      let message = messagebox.value;

      let sendMessageRequest ={    content: message};
      let sendMessageOptions ={    priority: 'High',    senderDisplayName : 'Jack'};
      let sendChatMessageResult = await chatThreadClient.sendMessage(sendMessageRequest, sendMessageOptions);
      let messageId = sendChatMessageResult.id;

      messages += '<div class="container darker">' + message + '</div>';
      messagesContainer.innerHTML = messages; 
      messagebox.value = '';
      console.log(`Message sent!, message id:${messageId}`);
  });
```

## Get a Teams meeting chat thread for a Communication Services user

The Teams meeting link and chat thread id can be retrieved using Graph APIs, detailed in [Graph documentation](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta). The Communication Services Calling SDK accepts a full Teams meeting link. This link is returned as part of the `onlineMeeting` resource, accessible under the [`joinWebUrl` property](/graph/api/resources/onlinemeeting?view=graph-rest-beta)
If you are using [Graph API](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta), you can also obtain the thread id. The response will have a `chatInfo` object that contains the `threadID`. 

You can also get the required meeting information and thread id from the **Join Meeting** URL in the Teams meeting invite itself.
A Teams meeting link looks like this: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here`. The Thread Id will be where `meeting_chat_thread_id` is in the link. Ensure that the `meeting_chat_thread_id` is unescaped before use. It should be in the following format: `19:meeting_ZWRhZDY4ZGUtYmRlNS00OWZaLTlkZTgtZWRiYjIxOWI2NTQ4@thread.v2`


## Run the code

Webpack users can use the `webpack-dev-server` to build and run your app. Run the following command to bundle your application host on a local webserver:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```

Open your browser and navigate to http://localhost:8080/. You should see the following:

:::image type="content" source="../media/javascript/acs-join-teams-meeting-quickstart.PNG" alt-text="Screenshot of the completed JavaScript Application.":::

Insert the Teams meeting link and thread id into the text boxes and press *Join Teams Meeting* to join the Teams meeting and chat from within your Communication Services application. Navigate to the bottom of the page and you can start chatting.

**Note** - Currently only sending, receiving and editing messages is supported for interoperability scenarios with Teams. Other features like typing indicators and Communication Services users adding or removing other users from the Teams meeting are not yet supported.  

