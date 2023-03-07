<template>
  <div class="TwilioDialer">
    <iframe
      ref="iframe"
      src="http://localhost:3000/agent-desktop"
      scrolling="no"
      style="height: 500px; width: 450px; margin-right: 50px; border: none"
      allow="microphone;camera">
    </iframe>
    <form @submit="handleFormSumbit" class="dialpad">
      <input class="input" type="text" v-model="phoneNumber" />
      <TwilioKeyPad @change="handleKeyboardChange"></TwilioKeyPad>
      <button class="callBtn" @click="dialANumber">Call</button>
    </form>
  </div>
</template>

<script>
import TwilioKeyPad from "./TwilioKeyPad";
const flexUrl = "http://localhost:3000/agent-desktop";

export default {
  name: "TwilioDialer",
  components: {
    TwilioKeyPad,
  },
  data() {
    return {
      phoneNumber: "",
    };
  },
  mounted() {
    window.addEventListener("message", this.receiveMessage);
  },
  methods: {
    handleKeyboardChange(e) {
      this.phoneNumber += e;
    },
    handleFormSumbit(e) {
      // eslint-disable-next-line
      debugger;
      e.preventDefault();
    },
    dialANumber() {
      let receiver = this.$refs.iframe.contentWindow;
      receiver.postMessage(this.phoneNumber, flexUrl);
    },
    receiveMessage(e) {
      // A function to handle sending messages.
      console.log("--------------------\nparent received: ", e.data);
      const payloadForCRM = e.data ? JSON.parse(e.data) : {};
      // check if event is for screenpop.
      // For more use cases you can send a key value-pair to identify the postmessage action instead of the logic below.

      if (typeof payloadForCRM.pop !== "undefined") {
        console.log("in this scenario we screenpop the record");
        // cleanParameterTable();
        this.screenPop(payloadForCRM);
      }
      //otherwise publish activity data
      else {
        this.activityData(payloadForCRM);
      }
    },
    screenPop(payloadForCRM) {
      console.log("screenPop received: ", payloadForCRM);
    },
    activityData(payloadForCRM) {
      console.log("activityData received: ", payloadForCRM);
    },
  },
};
</script>

<style scoped>
.TwilioDialer {
  display: grid;
  grid-template-columns: 1fr 1fr;
}
.TwilioDialer .dialpad {
  width: 300px;
  background-color: #f4f4f4;
  border: 1px solid rgb(224, 224, 224);
  padding: 25px;
  border-radius: 10px;
  text-align: center;
}

.TwilioDialer .input {
  border: 1px solid #cbccff;
  padding: 15px;
  display: block;
  width: 100%;
  margin-bottom: 25px;
  font-size: 1.2rem;
}

.TwilioDialer .callBtn {
  border: 1px solid green;
  padding: 10px 60px;
  border-radius: 50px;
  background-color: green;
  font-weight: 500;
  color: white;
  font-size: 1.25rem;
  margin-top: 20px;
  cursor: pointer;
}

.TwilioDialer .callBtn:hover {
  background-color: #005000;
  transition: background-color 0.5s ease;
}
</style>
