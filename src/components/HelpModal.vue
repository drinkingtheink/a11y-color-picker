<template>
  <div class="modal help">
      <div class="modal-content">
          <h2>Having trouble finding color combos?</h2>

          <p>It is possible to select colors that may take a while to find matches. Color choice, in combination with a high minimum contrast, can cause prolonged color searches. But, rest assured, eventually one will be found.</p>

          <h3>Don't want to wait? No problem.</h3>

          <p>The best thing to do is select a different color or adjust your minimum contrast (if you've made changes). If you choose a color closer to the extreme corners of the color wheel (see outlined areas in green), you're more likely to find matches for your preferred minimum contrast quicker.</p> 

          <figure>
            <img src="../assets/color-wheel-areas.png" alt="These areas of the color wheel offer the easiest to find color options at most contrast levels." />
            <figcaption>These areas of the color spectrum (outlined in green) will produce more combo possibilities. This is true of any color, not just the part of the light-blue spectrum shown here.</figcaption>
          </figure>

          <p>If you choose colors in the mid-range, it's harder to find contrasting colors.</p>
      
        <div class="modal-actions">
          <button @click="emitClose()">Back to Colors</button>
      </div>  
    </div>
  </div>
</template>

<script>
export default {
  name: 'HelpModal',
  methods: {
      emitClose() {
          this.$emit('closeModal');
      },
  },
}
</script>

<style> 
.modal {
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(0,0,0);
  background-color: rgba(0,0,0,0.4);
  font-size: 120%;
}

.modal-content {
  background-color: #fefefe;
  margin: 5% auto;
  padding: 2rem 4rem;
  border: 1px solid #888;
  width: 50vw;
  box-shadow: 0px 6px 9px 2px rgba(0,0,0,0.5);
}

.modal-content p, 
.modal-content h1, 
.modal-content h2, 
.modal-content h3, 
.modal-content span {
  text-align: left;
}

.modal-actions {
    padding-top: 2rem;
}

.modal img {
  max-width: 70%;
  margin: 0 auto;
  border: 5px solid rgba(0,0,0,0.4);
  border-radius: 20px;
}
</style>
