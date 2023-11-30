<template>
  <div class="hello">
    <h1>A11y Color Picker</h1>

    <label for="primary-color-select">Select your primary color:</label>
    <input type="color" id="primary-color-select" />
  </div>
</template>

<script>
export default {
  name: 'AppStage',
  props: {
    msg: String
  }
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
