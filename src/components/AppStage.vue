<template>
  <main class="hello">
    <h1>A11y Color Picker</h1>

    <section class="contrast-display">
      <label for="set-min-contrast">Set your minimum desired contrast (defaulted to a11y minimum):</label>
      <input type="range" id="min-contrast" name="min-contrast" min="4.4" max="16" :value="userMinThresh" @input="updateUserMinThresh" />
      <p>MIN CONTRAST: {{ userMinThresh }}</p>
    </section>

    <div class="color-config">
      <section class="color-select primary-select">
        <p>{{ !!primary ? `SELECTED:` : `SELECT A COLOR` }} {{ primary }}</p>
        <input v-show="!primary" type="color" id="primary-color-select" @input="handlePrimaryColorChange" />
        <p class="swatch" :style="`background-color: ${primary}`" @click="handlePrimaryClick" />
      </section>

      <p v-if="primary && secondary">CONTRAST: <span class="contrast-value">{{ contrast }}</span></p>

      <section class="color-select secondary-select">
        <p v-if="secondary">SECONDARY: {{ secondary }}</p>
        <div class="swatch" :style="`background-color: ${secondary}`">
          <button class="smol" @click="secondary = null">X</button>
        </div>
      </section>

    </div>
  </main>
  <ColorGen v-if="primary" :selectedColor="primary" @setSecondary="handleSecondaryColorChange" :a11yThresh="a11yThresh" :userMinThresh="userMinThresh" />
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
      userMinThresh: null,
    }
  },
  mounted() {
    this.userMinThresh = this.a11yThresh;
  },
  watch: {
    primary() {
			this.secondary = null;
    },
    userMinThresh() {
			this.secondary = null;
    },
		comparisonColor() {
			this.getColor();
		},
  },
  methods: {
    handlePrimaryColorChange(event) {
      this.primary = event.target.value;
    },
    handleSecondaryColorChange(color, contrast) {
      this.secondary = color;
      this.contrast = contrast;
    },
    updateUserMinThresh(event) {
      this.userMinThresh = Number(event.target.value);
    },
    handlePrimaryClick() {
      const swatch = document.getElementById('primary-color-select') || false;

      if (swatch) swatch.click();
    },
  },
}
</script>

<style>
:root {
  --borRad: 10px;
}

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

.color-config {
  display: flex;
  justify-content: space-between;
  width: 650px;
  margin: 0 auto;
  background: #eaeaea;
  padding-bottom: 2rem;
  border-radius: var(--borRad);
}

.color-config section {
  width: 30%;
}

.color-select .swatch {
  --swatchDim: 60px;

  height: var(--swatchDim);
  width: var(--swatchDim);
  margin: 0 auto;
  border: 5px solid rgba(0,0,0, 0.3);
  border-radius: 50%;
  position: relative;
}

.swatch button {
  position: absolute;
  top: 0; right: -30px;
}

.primary-select .swatch:hover {
  cursor: zoom-in;
}

.contrast-display label {
  display: block;
}

.contrast-display input {
  margin-left: 10px;
}

.contrast-value {
  display: block;
  font-size: 200%;
  margin-top: 1rem;
}
</style>
