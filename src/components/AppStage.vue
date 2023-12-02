<template>
  <main class="hello">
    <h1>A11y Color Picker</h1>

    <section class="primary-select">
      <label for="primary-color-select">Select your primary color:</label>
      <input type="color" id="primary-color-select" @input="handlePrimaryColorChange" />
      <p v-if="primary">SELECTED: {{ primary }}</p>
    </section>

    <section class="contrast-display">
      <label for="set-min-contrast">Set the minimum desired contrast</label>
      <input type="range" id="min-contrast" name="min-contrast" min="4.4" max="16" value="" />
      <p>CONTRAST: {{ contrast }}</p>
    </section>

    <section v-if="secondary" class="secondary-select">
      <p>SECONDARY: {{ secondary }} <span class="sec-display" :style="`background-color: ${secondary}`" /></p>
    </section>
  </main>
  <ColorGen v-if="primary" :selectedColor="primary" @setSecondary="handleSecondaryColorChange" :a11yThresh="a11yThresh" />
</template>

<script>
import ColorGen from './ColorGen.vue';

export default {
  name: 'AppStage',
  props: {
    msg: String
  },
  components: {
    ColorGen
  },
  data() {
    return {
      primary: null,
      secondary: null,
      contrast: null,
      a11yThresh: 4.4,
    }
  },
  methods: {
    handlePrimaryColorChange(event) {
      this.primary = event.target.value;
    },
    handleSecondaryColorChange(color, contrast) {
      this.secondary = color;
      this.contrast = contrast;
    },
  },
}
</script>

<style>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}

.sec-display {
  width: 40px;
  height: 40px;
  display: inline-block;
}
</style>
