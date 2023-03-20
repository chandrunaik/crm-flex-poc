<template>
  <div class="TwilioDialer">
    <iframe
      ref="iframe"
      src="http://localhost:3000/agent-desktop"
      scrolling="no"
      class="iframe-container"
      allow="microphone;camera">
    </iframe>
    <form @submit="handleFormSumbit" class="dialpad" :key="key">
      <template v-if="isFlexLoaded">
        <input class="input" type="text" v-model="phoneNumber" />
        <TwilioKeyPad @change="handleKeyboardChange"></TwilioKeyPad>
        <div>
          <button class="btn callBtn" @click="dialANumber">Call</button>
          <button class="btn muteBtn" @click="muteCall">Mute</button>
        </div>
      </template>
      <template v-else>
        <div class="loader">Initializing dialpad....</div>
      </template>
    </form>
    <div>
      <p>Agent Status: {{ visibility }}</p>
      <p>Call Status: {{ callStatus }}</p>
      <p>Call Details: {{ callDetails }}</p>
      <p>Mute: {{ muted }}</p>
      <button @click="toggleVisibility" class="btn">Toggle Visibility</button>
      <button @click="logoutFromFlex" class="btn">Logout from flex</button>
    </div>
  </div>
</template>

<script>
import TwilioKeyPad from "./TwilioKeyPad";
// const flexUrl = "http://localhost:3000/agent-desktop";

export default {
  name: "TwilioDialer",
  components: {
    TwilioKeyPad,
  },
  data() {
    return {
      phoneNumber: "+918971497427",
      isFlexLoaded: false,
      visibility: "",
      callStatus: "",
      key: "",
      muted: false,
      callDetails: {},
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
      e.preventDefault();
    },
    invokeAction(name, payload = null) {
      let receiver = this.$refs.iframe.contentWindow;

      let data = {
        name,
        payload,
      };

      receiver.postMessage(data, "*");
      console.log(">>> sending post message to flex-ui: ", data);
    },
    toggleVisibility() {
      // set Available
      if (this.visibility !== "Meeting") {
        this.invokeAction("SetActivity", { activityName: "Meeting" });
      } else {
        // set Unavailable
        this.invokeAction("SetActivity", { activityName: "Offline", rejectPendingReservations: true });
      }
    },
    dialANumber() {
      this.invokeAction("StartOutboundCall", {
        destination: this.phoneNumber,
        taskAttributes: {
          referenceIdentifier: "12345",
          namespace: "lp-xyz-gk",
        },
      });
    },
    muteCall() {
      this.invokeAction("ToggleMute");
    },
    logoutFromFlex() {
      this.invokeAction("Logout", { forceLogout: true });
    },
    receiveMessage(e) {
      try {
        const payloadForCRM = JSON.parse(e.data);

        // skip unknown messages
        if (!payloadForCRM.eventName) return;

        const eventName = payloadForCRM.eventName;

        switch (eventName) {
          case "pluginsInitialized":
            this.isFlexLoaded = true;
            break;
          case "afterSetActivity":
            this.visibility = payloadForCRM.payload.visibility;
            break;
          case "reservationCreated":
            this.callStatus = payloadForCRM.payload.status;
            this.key = +new Date();
            break;
          case "voice":
            break;
          case "toggleMute":
            this.muted = !this.muted;
            break;
          case "hangupCall":
            this.callDetails = payloadForCRM.payload;
            this.callStatus = "Disconnected";

            // fetch("https://holger-test.eu.ngrok.io", {
            //   method: "POST", // *GET, POST, PUT, DELETE, etc.
            //   mode: "cors", // no-cors, *cors, same-origin
            //   cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
            //   credentials: "same-origin", // include, *same-origin, omit
            //   headers: {
            //     "Content-Type": "application/json",
            //     // 'Content-Type': 'application/x-www-form-urlencoded',
            //   },
            //   redirect: "follow", // manual, *follow, error
            //   referrerPolicy: "no-referrer", // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
            //   body: JSON.stringify(payloadForCRM), // body data type must match "Content-Type" header
            // });

            break;
          default:
            console.log(">>> Unknown event received from flex-ui", eventName);
            return;
        }
      } catch (e) {
        console.log(">>> Error while processing flex-ui data in crm", e.message);
      }
    },
  },
};
</script>

<style scoped>
.TwilioDialer {
  display: grid;
  grid-template-columns: 1fr 350px 1fr;
  grid-gap: 25px;
}
.TwilioDialer .dialpad {
  background-color: #f4f4f4;
  border: 1px solid rgb(224, 224, 224);
  padding: 25px;
  border-radius: 10px;
  text-align: center;
}

.TwilioDialer .input {
  border: 1px solid #eee;
  padding: 15px;
  display: block;
  width: 100%;
  margin-bottom: 25px;
  font-size: 1.2rem;
}

.TwilioDialer .btn {
  border: 1px solid;
  font-weight: 500;
  color: white;
  font-size: 1.25rem;
  margin-top: 20px;
  border-radius: 50px;
  background: #aaa;
  padding: 10px;
  cursor: pointer;
}
.TwilioDialer .muteBtn {
  padding: 10px 60px;
  border-color: grey;
  background-color: grey;
}
.TwilioDialer .callBtn {
  padding: 10px 60px;
  border-color: green;
  background-color: green;
}

.TwilioDialer .callBtn:hover {
  background-color: #005000;
  transition: background-color 0.5s ease;
}
.iframe-container {
  height: 500px;
  border-radius: 20px;
  width: 100%;
  margin-right: 50px;
  border: none;
}
.loader {
  min-height: 100%;
  align-items: center;
  display: flex;
  justify-content: center;
}
</style>
