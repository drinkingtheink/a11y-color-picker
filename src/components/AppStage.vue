<template>
  <main class="fff">
    <h1>A11y Color Combos</h1>

    <section class="contrast-display">
      <label for="set-min-contrast">Set your minimum desired contrast (defaulted to a11y minimum):</label>
      <input 
        type="range" 
        id="min-contrast" 
        name="min-contrast" 
        min="4.4" 
        max="16" 
        step="0.1"
        :value="userMinThresh" 
        @input="updateUserMinThresh" 
      />
      <p class="min-contrast-display">MIN CONTRAST: {{ userMinThresh }}</p>
    </section>

    <div class="color-config">
      <section class="color-select primary-select">
        <p>{{ !!primary ? `PRIMARY:` : `SELECT A COLOR` }} <span class="value-display">{{ primary }}</span></p>
        <input v-show="!primary" type="color" id="primary-color-select" @input="handlePrimaryColorChange" />
        <p v-show="!!primary" class="swatch" :style="`background-color: ${primary}`" @click="handlePrimaryClick" />
      </section>

      <p>CONTRAST: <span class="contrast-value">{{ !!primary && !!secondary ? contrast : `??` }}</span></p>

      <section class="color-select secondary-select">
        <p>SECONDARY: <span class="value-display">{{ secondary }}</span></p>
        <div v-if="secondary" class="swatch" :style="`background-color: ${secondary}`">
          <button class="smol" @click="secondary = null">X</button>
        </div>
        <div v-if="!!primary && !secondary" class="select-secondary-prompt">
          <p>Select a color from the generated options.</p>
        </div>
      </section>

    </div>
  </main>
  <ColorGen 
    v-if="primary" 
    :selectedColor="primary" 
    :a11yThresh="a11yThresh" 
    :userMinThresh="userMinThresh"
    :lightOrDark="lightOrDark"
    @setSecondary="handleSecondaryColorChange" 
  />
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
      let root = document.documentElement;
      root.style.setProperty('--primary', this.primary);
			this.secondary = null;   
    },
    secondary() {
      let root = document.documentElement;
      root.style.setProperty('--secondary', this.secondary);
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
    lightOrDark(color) {
      if (!color) {
        return;
      }

      color = String(color);

      // Variables for red, green, blue values
      var r, g, b, hsp;
      
      // Check the format of the color, HEX or RGB?
      if (color.match(/^rgb/)) {

          // If RGB --> store the red, green, blue values in separate variables
          color = color.match(/^rgba?\((\d+),\s*(\d+),\s*(\d+)(?:,\s*(\d+(?:\.\d+)?))?\)$/);
          
          r = color[1];
          g = color[2];
          b = color[3];
      } 
      else {
          // If hex --> Convert it to RGB: http://gist.github.com/983661
          color = +("0x" + color.slice(1).replace( 
          color.length < 5 && /./g, '$&$&'));

          r = color >> 16;
          g = color >> 8 & 255;
          b = color & 255;
      }
      
      // HSP (Highly Sensitive Poo) equation from http://alienryderflex.com/hsp.html
      hsp = Math.sqrt(
      0.299 * (r * r) +
      0.587 * (g * g) +
      0.114 * (b * b)
      );

      // Using the HSP value, determine whether the color is light or dark
      if (hsp>127.5) {
          return 'light';
      } 
      else {
          return 'dark';
      }
    },
  },
}
</script>

<style>
:root {
  --borRad: 20px;
}

* {
  transition: all 0.2s;
}

main {
  background-color: var(--primary, white);
  padding: 1rem 0 2rem 0;
  border-bottom: 20px solid var(--secondary, white);
}

h1 {
  color: var(--secondary, #333);
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

button {
  background: #222;
  color: var(--primary, white);
  border: none;
  padding: 10px 20px;
  text-transform: uppercase;
  font-size: 1.2rem;
  border-radius: var(--borRad);
}

button:hover {
  cursor: pointer;
  color: #222;
  background-color: white;
}

.color-config {
  display: flex;
  justify-content: space-between;
  width: 650px;
  margin: 0 auto;
  background: #eaeaea;
  padding: 0 2rem 2rem 2rem;
  border-radius: var(--borRad);
  box-shadow: 0 5px 5px 0px rgba(0,0,0,0.5);
}

.color-config section {
  width: 30%;
}

.color-config label, .color-config p {
  font-weight: bold;
}

.color-select .swatch {
  --swatchDim: 60px;

  height: var(--swatchDim);
  width: var(--swatchDim);
  margin: 0 auto;
  border: 5px solid rgba(0,0,0, 0.3);
  border-radius: 50%;
  position: relative;
  transition: all 1s;
}

.swatch button {
  position: absolute;
  top: 0; right: -2.5rem;
  font-size: 0.8rem;
  padding: 5px 10px;
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
  font-size: 320%;
  margin-top: 2.5rem;
}

.value-display {
  display: block;
  padding: 0 1rem;
  background-color: white;
  font-weight: bold;
  font-size: 120%;
  border-radius: 5px;
}

.min-contrast-display {
  font-weight: bold;
}

.select-secondary-prompt {
  padding-top: 5%;
}
</style>
