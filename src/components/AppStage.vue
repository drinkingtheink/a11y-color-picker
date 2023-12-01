<template>
  <div class="hello">
    <h1>A11y Color Picker</h1>

    <section class="primary-select">
      <label for="primary-color-select">Select your primary color:</label>
      <input type="color" id="primary-color-select" @input="handlePrimaryColorChange" />
      <p>PRIMARY: {{ primary }}</p>
    </section>

    <section class="secondary-select">
      <label for="secondary-color-select">Select your secondary color:</label>
      <input type="color" id="secondary-color-select" @input="handleSecondaryColorChange" />
      <p>SECONDARY: {{ secondary }}</p>
    </section>
  </div>
</template>

<script>
export default {
  name: 'AppStage',
  props: {
    msg: String
  },
  data() {
    return {
      primary: null,
      secondary: null,
    }
  },
  methods: {
    handlePrimaryColorChange(event) {
      this.primary = event.target.value;
    },
    handleSecondaryColorChange(event) {
      this.secondary = event.target.value;
    },
  },
  computed: {
    selectionContrast() {
        let contrast = null;

        if (this.primary && this.secondary) {
            contrast = chroma.contrast(this.primary, this.secondary).toFixed(1);
        }

        return contrast;
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
</style>
