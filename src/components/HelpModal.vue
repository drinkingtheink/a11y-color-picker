<template>
  <div class="modal help">
      <div class="modal-content">
          <h2>Having trouble finding colors?</h2>

          <p>It is possible to select colors that may take a while to find matches for. That in combination with a high minimum contrast can cause prolonged color searches. But, rest assured, eventually one will be found.</p>

          <h3>Don't want to wait? No problem.</h3>

          <p>The best thing to do is select a different color. If you choose a color closer to the extremes of the color wheel, you're more likely to find matches for your preferred minimum contrast quicker.</p>
      </div>
  </div>
</template>

<script>
export default {
  name: 'HelpModal',
  props: {
    info: {
      type: Array
    },
  },
}
</script>

<style> 
/* The Modal (background) */
.modal {
  position: fixed; /* Stay in place */
  z-index: 1; /* Sit on top */
  left: 0;
  top: 0;
  width: 100%; /* Full width */
  height: 100%; /* Full height */
  overflow: auto; /* Enable scroll if needed */
  background-color: rgb(0,0,0); /* Fallback color */
  background-color: rgba(0,0,0,0.4); /* Black w/ opacity */
}

/* Modal Content/Box */
.modal-content {
  background-color: #fefefe;
  margin: 10% auto; /* 15% from the top and centered */
  padding: 20px;
  border: 1px solid #888;
  width: 80%; /* Could be more or less, depending on screen size */
}

</style>
