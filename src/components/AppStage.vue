<template>
  <main class="hello">
    <h1>A11y Color Picker</h1>

    <section class="primary-select">
      <label for="primary-color-select">Select your primary color:</label>
      <input type="color" id="primary-color-select" @input="handlePrimaryColorChange" />
      <p>PRIMARY: {{ primary }}</p>
    </section>
  </main>
  <ColorGen :selectedColor="primary" />
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
    }
  },
  methods: {
    handlePrimaryColorChange(event) {
      this.primary = event.target.value;
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
