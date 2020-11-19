<template>
  <div class="home">
    <div id="iframecontainer">
      <iframe
        id="myIframe"
        :src="signallingServerAddress"
        width="1920"
        height="1080"
      ></iframe>
    </div>
    <br /><br />

    <button @click="sendMessage">Send Message</button>
    <input type="text" v-model="jsonMessage.message" />
  </div>
</template>

<script>
export default {
  name: "Home",
  data: function () {
    return {
      // Json message to send to the unreal app
      jsonMessage: {
        type: "TestType",
        message: "Hello world",
      },
      signallingServerAddress: "http://localhost:80",
      iFrameScale: 0.75
    };
  },
  methods: {
    sendMessage: function () {
      console.log("Sending message from Vue...");
      // Send a json message via the iFrame
      document.getElementById("myIframe")
        .contentWindow.postMessage(JSON.stringify(this.jsonMessage), "*");
    },
    addListener: function () {
      // Adds a listener for any messages coming from the iFrame
      window.onmessage = function (e) {
        // This is where you will handle all return types, other messages may be sent to the window so you need to handle them accordingly.
        // As we are only expecting strings, ignore any objects
        if(typeof(e.data) !== 'object') {
          // E.g. ignore webpack messages
          if(!e.data.includes('webpack')) {
            alert("Message Recieved from Signalling Server: " + e.data);
          }
        }
      };
    },
    resizeIFrame: function() {
      document.getElementById("myIframe").width = window.innerWidth * this.iFrameScale;
      document.getElementById("myIframe").height = (window.innerWidth * this.iFrameScale) / 1.778;
    },
    addResizeListener: function() {
      window.addEventListener('resize', this.resizeIFrame);
    }
  },
  mounted: function () {
    // Add the listener for any messages coming from the unreal app
    this.addListener();
    this.addResizeListener();
    this.resizeIFrame();
  },
};
</script>

<style>
#myIframe {
  border: none;
  border-radius: 20px;
  box-shadow: -1px -1px 28px -6px rgba(0, 0, 0, 0.57);
}
</style>
