<template>
  <div class="home">
    <div id="iframecontainer">
      <iframe id="myIframe" src="http://localhost:80" :width="width" :height="height"></iframe>
    </div>
    <br/><br/>
      
    <button @click="sendMessage">Send Message</button>
    <input type="text" v-model="jsonMessage.message">
  </div>
</template>

<script>
export default {
  name: "Home",
  data: function() {
    return {
      // Json message to send to the unreal app
      jsonMessage: {
        type: "TestType",
        message: "Hello world"
      },
    }
  },
  computed: {
    width: function() {
      // Make the iframe 50% of the window width
      return window.innerWidth * 0.65;
    },
    height: function() {
      // Calculates a rough aspect ratio of 1920 / 1080
      return this.width / 1.778;
    }
  },
  methods: {
    sendMessage: function() {
      console.log("Sending message from Vue...");
      // Send a json message via the iFrame
      document.getElementById("myIframe").contentWindow.postMessage(JSON.stringify(this.jsonMessage),'*');
    },
    addListener: function() {
      // Adds a listener for any messages coming from the iFrame
      window.onmessage = function(e) {
        console.log("Message Recieved from Signalling Server: " + e.data);
      }
    }
  },
  mounted: function() {
    // Add the listener for any messages coming from the unreal app
    this.addListener();
  }
};
</script>

<style>
#myIframe {
  border: none;
  border-radius: 20px;
  box-shadow: -1px -1px 28px -6px rgba(0, 0, 0, 0.57);
}
</style>
