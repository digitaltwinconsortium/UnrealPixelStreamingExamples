# UnrealRenderStreamingExample
Sample project demonstrating how to set up unreal render streaming within a JavaScript framework

## Overview
Setting up pixel streaming with an iFrame is a pretty straight forward process, there really isn't much to it.
For demonstration this 

## (Front End) Send Message via iFrame
```javascript
// Send a json message via the iFrame
this.iFrame.contentWindow.postMessage(JSON.stringify(this.jsonMessage),'*');
```

## (Front End) Recieve Unreal Application Message
```javascript
// Adds a listener for any messages coming from the iFrame
window.onmessage = function(e) {
   console.log("Message Recieved from Signalling Server: " + e.data);
}
```

## (Signalling Server) Recieve iFrame Message
```javascript
window.onmessage = function(messageFromVue) {
   // Parse the json payload
   var jsonPayload = JSON.parse(messageFromVue.data);
   // If it is a message we want to send on to the Unreal Application
   if(jsonPayload.type == "TestType") {
      // Log the contents of the payload
      console.log("Message from VueJS recieved on Signalling Server: " + messageFromVue.data);
      // Send the payload  to the Unreal Application
      emitUIInteraction(jsonPayload);
  }
}
```

## (Signalling Server) Recieve Unreal Application Message
```javascript
function handleUnrealMessage(message) {
   // Log the message recieved
   console.log("Message recieved on signalling server from unreal: " + message);
   // Post the data back to the VueJS app
   window.top.postMessage(message, '*');
}
```

## (Unreal Application) Recieve Message Blueprint
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/UnrealRecieveMessageBP.png "Recieve Message Blueprint")

## (Unreal Application) Send Message Blueprint
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/UnrealSendMessageBP.png "Send Message Blueprint")

## (Unreal Application)Full Blueprint
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/UnrealFullBP.png "Full Blueprint")

## Send Message to Unreal Example
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/FromVue.png "Full Blueprint")

## Recieve Message from Unreal Example
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/FromUnreal.png "Full Blueprint")
