<template>
  <div class="color-tile">
  </div>
</template>

<script>
export default {
  name: 'Tile',
  props: {
		color: {
			type: String,
		},
  },
}
</script>

<style>
:root {
--tileDim: 20px;
}


.color-tile {
	height: var(--tileDim);
	width: var(--tileDim);
	border: 2px solid;
}
</style>
