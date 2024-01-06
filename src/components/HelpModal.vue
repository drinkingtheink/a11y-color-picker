<template>
  <div class="modal help">
      <div class="modal-content">
          <h2>Having trouble finding colors?</h2>

          <p>It is possible to select colors that may take a while to find matches for. Color choice in combination with a high minimum contrast can cause prolonged color searches. But, rest assured, eventually one will be found.</p>

          <h3>Don't want to wait? No problem.</h3>

          <p>The best thing to do is select a different color. If you choose a color closer to the extremes of the color wheel, you're more likely to find matches for your preferred minimum contrast quicker. If you choose colors in the mid-range, it's harder to find contrasting colors.</p>
      
        <div class="modal-actions">
          <button @click="emitClose()">Close Modal</button>
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
}

.modal-content {
  background-color: #fefefe;
  margin: 10% auto;
  padding: 2rem 4rem;
  border: 1px solid #888;
  width: 80%;
}

.modal-actions {
    padding-top: 2rem;
}
</style>
