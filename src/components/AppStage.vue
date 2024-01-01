<template>
  <main :class="[lightOrDark(base)]">
    <Lines v-show="!!base && !!overlay" class="lines-bg" />
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
        <p>{{ !!base ? `BASE COLOR:` : `SELECT A BASE COLOR:` }} <span class="value-display">{{ base }}</span></p>
        <p v-show="!!base" class="swatch" :style="`background-color: ${base}`" @click="handleBaseClick" />
        <code v-show="!!base" class="code">{{ baseCss }}</code>
        <input 
          type="color" 
          id="base-color-select" 
          @input="handleBaseColorChange" 
          :value="base || currentRandom"
          :class="[{ 'hide-me': !!base }]"
        />
        <p v-show="!base">Or Pick One of These:</p>

        <div v-show="!base" class="random-colors">
          <button 
            class="random-color" 
            v-for="color in randomColors" 
            :key="color"
            :style="`background-color: ${color}`" 
            @click="handleBaseColorChange({ 'target': { 'value': color}})"
          />
          <button class="more-randos mini" @click="makeRandomColorList()">More</button>
        </div>
      </section>

      <p :class="[{ 'hide-me-vis': !base || !overlay }]">CONTRAST: <span class="contrast-value">{{ !!base && !!overlay ? contrast : `??` }}</span></p>

      <section class="color-select overlay-select">
        <p>OVERLAY: <span class="value-display">{{ overlay }}</span></p>
        <div v-if="overlay" class="swatch" :style="`background-color: ${overlay}`">
          <button class="smol" @click="overlay = null">X</button>
        </div>
        <code v-show="!!overlay" class="code">{{ overlayCss }}</code>
        <div v-if="!!base && !overlay" class="select-overlay-prompt">
          <p>Select a color from the generated options.</p>
        </div>
        <div v-if="!base && !overlay" class="select-overlay-prompt">
          <p>Select a BASE color first.</p>
        </div>
      </section>

      <section class="color-actions">
        <button v-show="!!base && !!overlay" id="copy-css" @click="copyCssBlob">{{ copyCssVerb }}</button>
        <button v-show="!!base && !!overlay" id="swap" @click="swapBaseOverlay">Swap Colors</button>
        <button v-show="!!base && !!overlay" id="swap" @click="clearColors()">Clear Colors</button>
      </section>
    </div>
  </main>
  
  <ColorGen 
    v-if="base" 
    :class="lightOrDark(base)"
    :selectedColor="base" 
    :a11yThresh="a11yThresh" 
    :userMinThresh="userMinThresh"
    :lightOrDark="lightOrDark"
    @setOverlay="handleOverlayColorChange" 
  />
  
  <div class="halftone background" />
  
  <section 
    class="gallery" 
    v-show="!!base && !!overlay" 
    :class="lightOrDark(base)"
  >

    <h2>Examples Gallery</h2>

    <div class="gallery-grid">

      <div class="text text-1">
        <h3>Checkout this Headline</h3>
        <p>{{ blurb1 }}</p>

        <section class="graphs">
          <Graphs />
        </section>
      </div>

      <div class="abstract">
        <div class="pattern-banner pattern-banner-1" />
        <div class="shapes">
          <span class="triangle-up" />
          <span class="triangle-right" />
          <span class="triangle-down" />
          <span class="triangle-left" />
          <span class="triangle-up" />
          <span class="triangle-right" />
          <span class="circle" />
          <span class="square" />
        </div>

        <div class="gradients">
          <span class="gradient-1" />
          <span class="gradient-2" />
        </div>

        <div class="palette">
          <div class="swatch" 
              v-for="color in baseToOverlayPalette" 
              :key="color" 
              :style="`background-color: ${color}`"
            />
        </div>
        

        <section class="pull-quote">
          <blockquote>
            <p>"Any sufficiently advanced technology is indistinguishable from magic."</p>
            <footer><cite>- Arthur C. Clarke</cite></footer>
          </blockquote>
        </section>
      </div>
    </div>

    <div class="gallery-grid">
      <div class="fake-form">
        <label>How Are You?</label>
        <input type="text">

        <label>Another Text Input Example</label>
        <input type="text">
        
        <p>Please select your favorite Web language:</p>
        <input type="radio" id="html" name="fav_language" value="HTML">
        <label for="html">HTML</label><br>
        <input type="radio" id="css" name="fav_language" value="CSS">
        <label for="css">CSS</label><br>
        <input type="radio" id="javascript" name="fav_language" value="JavaScript">
        <label for="javascript">JavaScript</label>
      </div>
    </div>

    <div class="gallery-grid">
      <div class="blurb-2">
        <p>{{ blurb2 }}</p>
      </div>
    </div>
  </section>

  <nav v-show="!!base && !!overlay && showBottomNav" class="sticky">
    <p>BASE:</p>
    
    <section class="base">
      <p v-show="!!base" class="swatch" :style="`background-color: ${base}`" @click="handleBaseClick" />
    </section>
    
    <p>OVERLAY:</p>
    
    <section class="overlay">
      <p v-show="!!overlay" class="swatch" :style="`background-color: ${overlay}`" />
    </section>
    
    <button v-show="!!base && !!overlay" id="copy-css" @click="copyCssBlob">{{ copyCssVerb }}</button>
    
    <button v-show="!!base && !!overlay" id="swap" @click="swapBaseOverlay">Swap Colors</button>

    <button v-show="!!base && !!overlay" id="swap" @click="clearColors()">Clear Colors</button>
  </nav>
</template>

<script>
import chroma from "chroma-js";
import ColorGen from './ColorGen.vue';
import Graphs from './illu/Graphs.vue';
import Lines from './illu/Lines.vue';

export default {
  name: 'AppStage',
  props: {
    msg: String
  },
  components: {
    ColorGen,
    Graphs,
    Lines,
  },
  data() {
    return {
      base: null,
      overlay: null,
      contrast: null,
      a11yThresh: 4.4,
      userMinThresh: null,
      copyCssVerb: 'copy CSS',
      blurb1: '"While color contrast is often primarily an aesthetic choice, the use of color on a website pertains to using color to communicate information. WCAG guideline 1.4.1 on the use of color requires that "color is not used as the only visual means of conveying information, indicating an action, prompting a response, or distinguishing a visual element."',
      blurb2: '"Having good color contrast on your site benefits all your users, but it is particularly beneficial to users with certain types of color blindness and other similar conditions, who experience low contrast, and have trouble differentiating between similar colors. This is because they don\'t see bright and dark areas as readily as those without such conditions, and therefore have trouble seeing edges, borders, and other details."',
      wipeOverlay: true,
      randomColors: [],
      currentRandom: null,
      colorPickerInst: null,
      showBottomNav: false,
      baseToOverlayPalette: [],
    }
  },
  created() {
    window.addEventListener('scroll', this.handleScroll);
  },
  mounted() {
    this.userMinThresh = this.a11yThresh;

    window.addEventListener('load', this.findColorPicker, false);

    this.makeRandomColorList();

    this.getRando();

    this.makeBaseToOverlayPalette(this.base, this.overlay);

  },
  unmounted() {
    window.removeEventListener('load', this.findColorPicker, false);
    window.removeEventListener('scroll', this.handleScroll);
  },
  watch: {
    base() {
      let root = document.documentElement;
      root.style.setProperty('--base', this.base);
      
      if (this.wipeOverlay) { 
        this.overlay = null;
        this.wipeOverlay = true;
      }

      if (!!this.base && !!this.overlay) {
        this.makeBaseToOverlayPalette(this.base, this.overlay);
      }
    },
    overlay() {
      let root = document.documentElement;
      root.style.setProperty('--overlay', this.overlay);

      if (!!this.base && !!this.overlay) {
        this.makeBaseToOverlayPalette(this.base, this.overlay);
      }
    },
    userMinThresh() {
			this.overlay = null;
    },
		comparisonColor() {
			this.getColor();
		},
  },
  methods: {
    makeBaseToOverlayPalette(base, overlay) {
      
      if (base && overlay) {
        const baseColor = chroma(String(base));
        const overlayColor = chroma(String(overlay)); 

        for (var i = 0; i < 5; i++) {
            this.baseToOverlayPalette[i] = chroma.mix(baseColor, overlayColor, i * 0.25).hex()
        }
      }
    },
    handleScroll() {
      const distFromTop = 600;

      if (window.scrollY > distFromTop) {
        this.showBottomNav = true;
      } else if (window.scrollY <= distFromTop) {
        this.showBottomNav = false;
      }
    },
    clearColors() {
      this.overlay = null;
      this.base = null;
    },
    findColorPicker() {
      let colorPicker;

      colorPicker = this.colorPickerInst = document.querySelector('#base-color-select');
      colorPicker.value = chroma.random().hex();
      colorPicker.select();
    },
    makeRandomColorList() {
      this.randomColors = [];

      for (let step = 0; step < 5; step++) {
        const newColor = chroma.random().hex();
        this.randomColors.push(newColor);
      }
    },
    handleBaseColorChange(event) {
      this.base = event.target.value;

      this.overlay = null;
    },
    handleOverlayColorChange(color, contrast) {
      this.overlay = color;
      this.contrast = contrast;
    },
    swapBaseOverlay() {
      const currOverlay = String(this.overlay);
      const currBase = String(this.base);

      this.wipeOverlay = false;
      this.base = currOverlay;
      this.handleOverlayColorChange(currBase, this.contrast);
    },
    copyCssBlob() {
      navigator.permissions.query({ name: 'clipboard-write' }).then((result) => {
        if (result.state === 'granted' || result.state === 'prompt') {
          navigator.clipboard.writeText(this.cssBlob);
          // alert uses it's been copied

          this.copyCssVerb = 'COPIED!';

          setTimeout(() => {
            this.copyCssVerb = 'Copy CSS';
          }, '2000');
        }
      });
    },
    updateUserMinThresh(event) {
      this.userMinThresh = Number(event.target.value);
    },
    handleBaseClick() {
      const swatch = document.getElementById('base-color-select') || false;

      if (swatch) swatch.click();

      window.scrollTo(0,0);
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
    getRando() {
      this.currentRandom = chroma.random().hex();
    },
  },
  computed: {
    cssBlob() {
      const blob = `
:root {
  --base: ${this.base};
  --overlay: ${this.overlay};
}

h1, h2, h3, h4, h5, p, span, div {
  color: var(--overlay);
}

.base {
  background-color: var(--base);
}
`

      return blob;
    },
    baseCss() {
      return `--base: ${String(this.base).toUpperCase()}` || null;
    },
    overlayCss() {
      return `--overlay: ${String(this.overlay).toUpperCase()}` || null;
    },
  },
}
</script>

<style>

.lines-bg {
  position: absolute;
  width: 120%;
  height: 39rem;
  top: 5px;
  left: -20px;
  z-index: 1;
  pointer-events: none;
}

nav {
  background: linear-gradient(to bottom, var(--panelBg) 16%,#919191 100%);
  padding: 10px 2rem 0 2rem;
  text-align: center;
  width: 50%;
  position: fixed;
  z-index: 2;
  height: 80px;
  border-radius: 10px 10px 0 0;
  border: 1px solid #222;
  display: flex;
  justify-content: center;
  align-items: flex-start;
  left: 50%;
  bottom: 1.5rem;
  transform: translate(-50%, 50%);
}

nav * {
  margin-right: 10px;
}

nav p {
  padding: 0;
  margin: 0 10px 0 0;
}

nav button {
  transform: scale(0.6);
  width: 100%;
}

:root {
  --borRad: 20px;
  --panelBg: #eaeaea;
}

.gradients {
  width: 100%;
  display: flex;
  justify-content: space-evenly;
  margin-top: 2rem;
  padding: 0 0 2rem 0 !important;
}

.gradient-1,
.gradient-2 {
  display: inline-block;
  width: 46%;
  height: 200px;
  border: 2px solid var(--overlay);
  margin: 0 5px;
  border-radius: 5px;
}

.gradient-1 {
  background-image: linear-gradient(to right top, var(--overlay), var(--base));
}

.gradient-2 {
  background-image: linear-gradient(to left bottom, var(--overlay), var(--base));
}

.palette {
  display: flex;
  height: 6rem;
  padding: 0 0 2rem 1rem !important;
}

.palette .swatch {
  border: 2px solid var(--overlay);
  margin-right: 2px;
  padding: 0;
  height: 5rem;
  width: 19%;
}

.abstract {
  width: 45%;
  padding: 0 !important;
}

.shapes {
  display: flex;
  width: 100%;
  justify-content: space-evenly;
  padding: 0.5rem 0 0.5rem 0 !important;
  margin-top: 1rem;

  --triSide: 20px;
  --triBot: 35px;
}

.shapes .triangle-up {
  width: 0;
  height: 0;
  border-left: var(--triSide) solid transparent;
  border-right: var(--triSide) solid transparent;
  border-bottom: var(--triBot) solid var(--overlay);
}

.shapes .triangle-down {
  width: 0;
  height: 0;
  border-left: var(--triSide) solid transparent;
  border-right: var(--triSide) solid transparent;
  border-top: var(--triBot) solid var(--overlay);
}

.shapes .triangle-left {
  width: 0;
  height: 0;
  border-top: var(--triSide) solid transparent;
  border-right: var(--triBot) solid var(--overlay);
  border-bottom: var(--triSide) solid transparent;
}

.shapes .triangle-right {
  width: 0;
  height: 0;
  border-top: var(--triSide) solid transparent;
  border-left: var(--triBot) solid var(--overlay);
  border-bottom: var(--triSide) solid transparent;
}

.shapes .circle {
  width: var(--triBot);
  height: var(--triBot);
  background-color: var(--overlay);
  border-radius: 50%;
}

.shapes .square {
  width: var(--triBot);
  height: var(--triBot);
  background-color: var(--overlay);
}

.pull-quote {
  font-size: 200%;
  background-color: var(--overlay);
  color: var(--base);
  padding: 1rem 0;
  border-radius: 100px 100px 0 100px;
}

.pull-quote p {
  margin-bottom: 0;
  color: var(--base) !important;
  text-align: left;
}

.pull-quote footer {
  padding-top: 20px;
  font-style: italic;
}

.fake-form {
  width: 45%;
  text-align: left;
}

.fake-form label {
  display: block;
  margin-bottom: 0.5rem;
  text-transform: uppercase;
}

.fake-form input {
  background-color: transparent;
  border: 2px solid var(--overlay);
  color: var(--overlay);
  padding: 10px;
  border-radius: 5px;
  width: 100%;
  font-size: 150%;
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
  padding-top: 2rem;
  padding-bottom: 20rem;
  position: relative;
  z-index: 1;
}

.gallery p, .gallery span, .gallery div {
  color: var(--overlay);
}

.gallery h2 {
  padding-bottom: 2rem;
}

.dark.gallery h2 {
  color: white;
}

.gallery h3 {
  font-size: 180%;
  border-bottom: 7px solid var(--overlay);
  padding-bottom: 0.5rem;
  padding-top: 0;
}

.gallery div {
  padding: 0 2rem;
}

.gallery input {
  margin-bottom: 1rem;
}

.gallery-grid {
  display: flex;
}

.text-1 {
  background-color: var(--base);
  text-align: left;
  width: 50%;
  font-size: 160%;
}

.text-1 h3, .text-1 p {
  color: var(--overlay);
  padding-top: 0;
  margin-top: 1rem;
}

.pattern-banner-1 {
  height: 3rem;
  width: 100%;
  background-color: transparent;
  background-image: linear-gradient(45deg, transparent 25%, var(--overlay) 25%, var(--overlay) 50%, transparent 50%, transparent 75%, var(--overlay) 75%, var(--overlay) 100%);
  background-size: 56.57px 56.57px;
  padding: 0 !important;
}

* {
  transition: all 0.2s;
}

main {
  background-color: var(--base, transparent);
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
  color: white;
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
  background: var(--panelBg);
  padding: 0 2rem 1rem 2rem;
  border-radius: var(--borRad);
  box-shadow: 0 5px 5px 0px rgba(0,0,0,0.5);
  position: relative;
  z-index: 1;
}

.color-config section {
  width: 30%;
}

.color-config section.color-actions {
  width: 100%;
  margin: 0 auto;
  height: 40px;
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

nav .swatch {
  --swatchDim: 30px;

  height: var(--swatchDim);
  width: var(--swatchDim);
  border: 5px solid rgba(0,0,0, 0.3);
  border-radius: 50%;
  transition: all 1s;
}

nav .swatch:hover {
  cursor: zoom-in;
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
  background: #666666;
  background: linear-gradient(180deg, #666666, #222222);
  margin: 0 auto;
  width: 300px;
  padding: 5px;
  color: white;
  border-radius: 10px 10px 0 0;
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
  background-color: var(--base, transparent);
}

.color-generator {
  margin-top: -2rem;
  padding-bottom: 1rem;
}

.dark .options-header {
  color: white;
}

.graphs * {
  fill: var(--overlay);
}

.random-colors {
  display: flex;
  justify-content: space-between;
  padding-top: 5px;
  position: relative;
}

.random-color {
  --swatchDim: 30px;

  height: var(--swatchDim);
  width: var(--swatchDim);
  border-radius: 50%;
  margin-right: 5px;
  padding: 0;
  border: 4px solid rgba(0,0,0, 0.3);
}

input[type="color"] {
  width: 100%;
}

.more-randos {
  transform: scale(0.5);
  width: 100%;
  position: absolute;
  bottom: -50px;
}

button.mini {
  background-color: rgba(0,0,0,0.5);
  color: white;
  transition: all 0.2s;
}

button.mini:hover {
  background-color: rgba(0,0,0,1);
}

.halftone {
  min-height: 2rem;
  background-image: radial-gradient(
      circle at center,
      var(--overlay) 0.25rem,
      transparent 0
    ), radial-gradient(circle at center, var(--overlay) 0.25rem, transparent 0);
  background-size: 1.3rem 1.3rem;
  background-position: 0 0, 0.65rem 0.65rem;
}

.halftone.background {
  position: absolute;
  top: 45rem;
  left: -10%;
  width: 130%;
  height: 80rem;
  transform: rotate(-5deg);
  z-index: 0;
  pointer-events: none;
  opacity: 0.3;
  padding: 2rem 0;
  border-top: 10px solid var(--overlay);
  border-bottom: 10px solid var(--overlay);
}

.hide-me {
  height: 0;
  visibility: hidden;
  padding: 0;
  margin: 0;
}

.hide-me-vis {
  visibility: hidden;
}

/** lines bg */

@keyframes dash {
  to {
    stroke-dashoffset: 0;
  }
}

.lines-bg {
  opacity: 0.6;
}

.lines-bg path {
  stroke-dasharray: 1500;
  stroke-dashoffset: 1500;
  animation: dash 10s linear alternate infinite;
}

.cls-1{
    stroke: var(--overlay)
}
.cls-1,.cls-2,.cls-3,.cls-4,.cls-5,.cls-6,.cls-7,.cls-8,.cls-9,.cls-10,.cls-11,.cls-12,.cls-13,.cls-14,.cls-15,.cls-16,.cls-17,.cls-18,.cls-19,.cls-20,.cls-21,.cls-22{
    fill:none;
    stroke-miterlimit:10;
}
.cls-2{
    stroke: var(--overlay)
}
.cls-3{
    stroke: var(--overlay)
}
.cls-4{
    stroke: var(--overlay)
}
.cls-5{
    stroke: var(--overlay)
}
.cls-6{
    stroke: var(--overlay)
}
.cls-7{
    stroke: var(--overlay)
}
.cls-8{
    stroke: var(--overlay)
}
.cls-9{
    stroke: var(--overlay)
}
.cls-10{
    stroke:#fff;
}
.cls-11{
    stroke: var(--overlay)
}
.cls-12{
    stroke: var(--overlay)
}
.cls-13{
    stroke: var(--overlay)
}
.cls-14{
    stroke: var(--overlay)
}
.cls-15{
    stroke: var(--overlay)
}
.cls-16{
    stroke: var(--overlay)
}
.cls-17{
    stroke: var(--overlay)
}
.cls-18{
    stroke: var(--overlay)
}
.cls-19{
    stroke: var(--overlay)
}
.cls-20{
    stroke: var(--overlay)
}
.cls-21{
    stroke: var(--overlay)
}
.cls-22{
    stroke: var(--overlay)
} 
</style>
