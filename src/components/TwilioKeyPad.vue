<template>
  <div class="TwilioKeyboard" @click="handleKeyboardEnter">
    <div class="key">1</div>
    <div class="key">2</div>
    <div class="key">3</div>
    <div class="key">4</div>
    <div class="key">5</div>
    <div class="key">6</div>
    <div class="key">7</div>
    <div class="key">8</div>
    <div class="key">9</div>
    <div class="key">*</div>
    <div class="key">+</div>
    <div class="key">#</div>
  </div>
</template>

<script>
export default {
  name: "TwilioKeyPad",
  methods: {
    handleKeyboardEnter(e) {
      if (e.target.classList.contains("key")) {
        this.$emit("change", e.target.innerText);
      }
    },
  },
};
</script>

<style scoped>
.TwilioKeyboard {
  display: grid;
  grid-template-columns: repeat(3, 75px);
  grid-template-rows: repeat(4, 75px);
  font-size: 1.5rem;
  color: #454545;
  grid-gap: 10px;
  font-weight: 600;
  justify-content: center;
}

.TwilioKeyboard .key {
  cursor: pointer;
  border-radius: 50%;
  line-height: 75px;
  background: white;
}

.TwilioKeyboard .key:hover {
  background-color: #c4ccff;
  transition: background-color 0.5s ease;
}
</style>
