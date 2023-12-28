<template>
  <main :class="lightOrDark(base)">
    <div class="top-border" />
    <h1>A11y Color Combinator</h1>

    <section class="contrast-display">
      <label for="set-min-contrast">Set your minimum desired contrast (cannot go below WCAG minimum):</label>
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
      <section class="color-select base-select">
        <p>{{ !!base ? `BASE COLOR` : `SELECT A COLOR` }} <span class="value-display">{{ base }}</span></p>
        <input v-show="!base" type="color" id="base-color-select" @input="handleBaseColorChange" />
        <p v-show="!!base" class="swatch" :style="`background-color: ${base}`" @click="handleBaseClick" />
      </section>

      <p>CONTRAST: <span class="contrast-value">{{ !!base && !!overlay ? contrast : `??` }}</span></p>

      <section class="color-select overlay-select">
        <p>OVERLAY: <span class="value-display">{{ overlay }}</span></p>
        <div v-if="overlay" class="swatch" :style="`background-color: ${overlay}`">
          <button class="smol" @click="overlay = null">X</button>
        </div>
        <div v-if="!!base && !overlay" class="select-overlay-prompt">
          <p>Select a color from the generated options.</p>
        </div>
      </section>

      <section class="color-actions">
        <button v-show="!!base && !!overlay" id="copy-css">Copy CSS</button>
        <button v-show="!!base && !!overlay" id="swap">Swap Base and Overlay</button>
      </section>
    </div>
  </main>
  <ColorGen 
    v-if="base" 
    :selectedColor="base" 
    :a11yThresh="a11yThresh" 
    :userMinThresh="userMinThresh"
    :lightOrDark="lightOrDark"
    @setOverlay="handleOverlayColorChange" 
  />
  <section class="gallery" v-show="!!base && !!overlay">
    <h2>Examples Gallery</h2>

    <div class="text text-1">
      <h3>Checkout this Headline</h3>
      <p>{{ blurb1 }}</p>
    </div>
  </section>
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
      base: null,
      overlay: null,
      contrast: null,
      a11yThresh: 4.4,
      userMinThresh: null,
      blurb1: '"While color contrast is often primarily an aesthetic choice, the use of color on a website pertains to using color to communicate information. WCAG guideline 1.4.1 on the use of color requires that "color is not used as the only visual means of conveying information, indicating an action, prompting a response, or distinguishing a visual element."',
    }
  },
  mounted() {
    this.userMinThresh = this.a11yThresh;
  },
  watch: {
    base() {
      let root = document.documentElement;
      root.style.setProperty('--base', this.base);
			this.overlay = null;   
    },
    overlay() {
      let root = document.documentElement;
      root.style.setProperty('--overlay', this.overlay);
    },
    userMinThresh() {
			this.overlay = null;
    },
		comparisonColor() {
			this.getColor();
		},
  },
  methods: {
    handleBaseColorChange(event) {
      this.base = event.target.value;
    },
    handleOverlayColorChange(color, contrast) {
      this.overlay = color;
      this.contrast = contrast;
    },
    updateUserMinThresh(event) {
      this.userMinThresh = Number(event.target.value);
    },
    handleBaseClick() {
      const swatch = document.getElementById('base-color-select') || false;

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
  transition: all 0.3s;
}

.top-border {
  height: 10px;
  background-color: var(--overlay, --base, transparent);
}

h1 {
  color: var(--overlay, #222);
}

.dark h1 {
  color: white;
}

.gallery {
  width: 80%;
  margin: 0 auto;
  padding-bottom: 20rem;
}

.gallery div {
  padding: 2rem;
}

.text-1 {
  background-color: var(--base);
  text-align: left;
  width: 450px;
  font-size: 120%;
}

.text-1 h3, .text-1 p {
  color: var(--overlay);
  padding-top: 0;
  margin-top: 1rem;
}

* {
  transition: all 0.2s;
}

main {
  background-color: var(--base, white);
  padding-bottom: 2rem;
}

h1 {
  color: var(--overlay, #333);
}

h3 {
  margin: 40px 0 0;
}

h1, h2, h3, h4, h5 {
  text-transform: uppercase;
  font-weight: bold;
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
  color: var(--overlay, white);
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
  flex-wrap: wrap;
  justify-content: space-between;
  width: 650px;
  margin: 0 auto;
  background: #eaeaea;
  padding: 0 2rem 1rem 2rem;
  border-radius: var(--borRad);
  box-shadow: 0 5px 5px 0px rgba(0,0,0,0.5);
}

.color-config section {
  width: 30%;
}

.color-config section.color-actions {
  width: 100%;
  margin: 0 auto;
}

.color-actions button {
  transform: scale(0.8);
}

label, p {
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

.base-select .swatch:hover {
  cursor: zoom-in;
}

.contrast-display label {
  display: block;
}

.dark .contrast-display label {
  color: white;
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
  text-transform: uppercase;
}

.min-contrast-display {
  font-weight: bold;
}

.dark .min-contrast-display {
  color: white;
}

.select-overlay-prompt {
  padding-top: 5%;
}

#app,
.color-generator,
.gallery {
  background-color: var(--base, #FFF);
}

.color-generator {
  padding-bottom: 1rem;
}

</style>
