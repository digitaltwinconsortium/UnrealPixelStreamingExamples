# UnrealRenderStreamingExample

Sample project demonstrating how to set up unreal render streaming within a JavaScript framework.

This project is supposed to be a basic walkthrough of setting up the Unreal Pixel streaming within an iFrame rather than a fully fleshed out project. It should be used as a basis build on top of.

## Overview

Setting up pixel streaming with an iFrame is a pretty straight forward process, there really isn't much to it.
For demonstration this simple Vue.js app includes an iFrame to the default pixel streaming example provided by unreal, and uses the JavaScript method .postMessage() and standard event listeners to allow cross-origin communication between the Vue app and Signalling Server. In this example we're sending a custom message to the Signalling Server using a simple button click event as the trigger. We're also adding an event listener on the mounted Vue lifecycle hook to recieve messages sent from the Signalling Server to the Vue front end, this is logged out and set up as an alert, but should provide enough context to use the response in any capacity within the Vue app.

The iFrame aspect ratio is maintained by using bound computed properties for the width and height.
Scene interactivity is handled by the Signalling Server within the iFrame content window, this can be seen as the cursor crosses the boundary between Vue app and iFrame. This cursor behaviour is enabled by the Singalling Server using the inputOptions object with a choice between LockedMouse and HoveringMouse, using HoveringMouse removes the need to click on the iFrame to enable interactions.

The unreal application itself is set up in the same way it would to handle any pixel streaming input / output. The example we use for this project simply logs a message recieved and sends the name of the object clicked. Below we include screenshots of the blueprints used to manage the scene (all of it is simply placed in the level blueprint), however for full documentation on the unreal application setup; the documentation found [here.](https://docs.unrealengine.com/en-US/Platforms/PixelStreaming/index.html)


## (Front End) Send Message via iFrame
Below is an example of how to send the message via the iFrame.

```javascript
// Send a json message via the iFrame
document.getElementById("myIframe").contentWindow.postMessage(JSON.stringify(this.jsonMessage), "*");
```

In our version we use a basic JSON object with a type property to allow more flexible handling of the data
```javascript
window.onmessage = function(messageFromVue) {
   console.log("Message recieved on Signalling Server: " + messageFromVue.data);
   var jsonPayload = JSON.parse(messageFromVue.data);
   if(jsonPayload.type == "TestType") {
      emitUIInteraction(jsonPayload);
   }
}
```


## (Front End) Recieve iFrame Message
To recieve data sent from the signalling server, simply add a listener to the window. The window may recieve messages from various sources, so it is worthwhile adding in checks in order to handle the data appropriately.
```javascript
// Adds a listener for any messages coming from the iFrame
window.onmessage = function (e) {
  console.log("Message Recieved from Signalling Server: " + e.data);
};
```

## (Signalling Server) Recieve iFrame Message
Recieving data from the VueJS application can be achieved in the same way as shown above, simply add in a listener to the window. As stated, the window may recieve messages from various sources so it is worthwhile adding in checks & try catches as appropriate. The content can be added in anywhere within the app.js within the Unreal Signalling server where it will be called. For a windows build with Pixel Streaming enabled, this can be found (as of version 4.25) in: **WindowsNoEditor\WindowsNoEditor\Engine\Source\Programs\PixelStreaming\WebServers\SignallingWebServer\scripts**
```javascript
window.onmessage = function (messageFromVue) {
  // Parse the json payload
  var jsonPayload = JSON.parse(messageFromVue.data);
  // If it is a message we want to send on to the Unreal Application
  if (jsonPayload.type == "TestType") {
    // Log the contents of the payload
    console.log("Message from VueJS recieved on Signalling Server: " + messageFromVue.data);
    // Send the payload  to the Unreal Application
    emitUIInteraction(jsonPayload);
  }
};
```

## (Signalling Server) Recieve Message from Unreal and Send Message via iFrame
To recieve messages from the unreal application, call the existing method within app.js called "addResponseEventListener", naming the listener and providing a method to handle the event. We simply added our call to the bottom of the load() method. From this example, the message will simply be a string related to the name of the object clicked in the unreal scene, however it can be a json object if required.
```javascript
function handleUnrealMessage(message) {
  // Log the message recieved
  console.log("Message recieved on signalling server from unreal: " + message);
  // Post the data back to the VueJS app
  window.top.postMessage(message, "*");
}

function load() {
   setupHtmlEvents();
   setupFreezeFrameOverlay();
   registerKeyboardEvents();
   start();
   addResponseEventListener("handle_responses", handleUnrealMessage);
}
```

## (Signalling Server) Cursor Behavior
As mentioned above, by setting the control scheme type (found in app.js) within the inputOptions to HoveringMouse; you will not need to click on the iFrame to begin interacting if that is desired.
```javascript
var inputOptions = {
  controlScheme: ControlSchemeType.HoveringMouse,
};
```

## (Unreal Application) Recieve Message Blueprint
The recieve message blueprint (added to the level blueprint) is very similar to what can be found in the documentation for pixel streaming on the Unreal docs, differing only by printing a message sent via VueJS to the screen.
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/UnrealRecieveMessageBP.png "Recieve Message Blueprint")

## (Unreal Application) Send Message Blueprint
The send message blueprint (also added to the level blueprint) differs slightly from what is found in the Unreal docs, simply getting the name of a clicked actor within the scene and sending back its string name. As mentioned in the unreal docs, this can be a json string also if you want to be able to handle more complex object structures.
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/UnrealSendMessageBP.png "Send Message Blueprint")

## (Unreal Application)Full Blueprint
Exactly the same functionality as the two blueprints above simply merged into the same blueprint to handle both sending and recieving data from the signalling server.
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/UnrealFullBP.png "Full Blueprint")

## Send Message to Unreal Example

![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/FromVue.png "Full Blueprint")

## Recieve Message from Unreal Example
For clearer images of the example working see [here.](https://github.com/Slingshot-Simulations/UnrealPixelStreamingExamples/tree/IFrame-Example/ReferenceImages)
![alt text](https://github.com/Slingshot-Simulations/UnrealRenderPixelExample/blob/IFrame-Example/ReferenceImages/FromUnreal.png "Full Blueprint")
