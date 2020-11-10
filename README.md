# UnrealRenderStreamingExample

Sample project demonstrating how to set up unreal render streaming within a JavaScript framework

## Overview

Setting up pixel streaming with an iFrame is a pretty straight forward process, there really isn't much to it.
For demonstration this simple Vue.js app includes an iFrame to the default pixel streaming example provided by unreal, and uses the JavaScript method .postMessage() and standard event listeners to allow cross-origin communication between the Vue app and Signalling Server. In this example we're sending a custom message to the Signalling Server using a simple button click event as the trigger. We're also adding an event listener on the mounted Vue lifecycle hook to recieve messages sent from the Signalling Server to the Vue front end, this is logged out and set up as an alert, but should provide enough context to use the response in any capacity within the Vue app.

The iFrame aspect ratio is maintained by using bound computed properties for the width and height.
Scene interactivity is handled by the Signalling Server within the iFrame content window, this can be seen as the cursor crosses the boundary between Vue app and iFrame. This cursor behaviour is enabled by the Singalling Server using the inputOptions object with a choice between LockedMouse and HoveringMouse, using HoveringMouse removes the need to click on the iFrame to enable interactions.

## (Front End) Send Message via iFrame

```javascript
// Send a json message via the iFrame
this.iFrame.contentWindow.postMessage(JSON.stringify(this.jsonMessage), "*");
```

## (Front End) Recieve Unreal Application Message

```javascript
// Adds a listener for any messages coming from the iFrame
window.onmessage = function (e) {
  console.log("Message Recieved from Signalling Server: " + e.data);
};
```

## (Signalling Server) Recieve iFrame Message

```javascript
window.onmessage = function (messageFromVue) {
  // Parse the json payload
  var jsonPayload = JSON.parse(messageFromVue.data);
  // If it is a message we want to send on to the Unreal Application
  if (jsonPayload.type == "TestType") {
    // Log the contents of the payload
    console.log(
      "Message from VueJS recieved on Signalling Server: " + messageFromVue.data
    );
    // Send the payload  to the Unreal Application
    emitUIInteraction(jsonPayload);
  }
};
```

## (Signalling Server) Recieve Unreal Application Message

```javascript
function handleUnrealMessage(message) {
  // Log the message recieved
  console.log("Message recieved on signalling server from unreal: " + message);
  // Post the data back to the VueJS app
  window.top.postMessage(message, "*");
}
```

## (Signalling Server) Cursor Behavior

```javascript
var inputOptions = {
  controlScheme: ControlSchemeType.HoveringMouse,
};
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
