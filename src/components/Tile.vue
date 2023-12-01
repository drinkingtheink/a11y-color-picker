<template>
  <div class="color-tile" :style="`background-color: ${myColor}`">
  </div>
</template>

<script>
export default {
  name: 'Tile',
  props: {
		comparisonColor: {
			type: String,
		},
		a11yThresh: {
			type: Number,
		},
  },
	data() {
		return {
			myColor: null,
		}
	},
	methods: {
		getColor() {
			this.myColor = 'red';
		}
	},
	mounted() {
		this.getColor();
	}
}
</script>

<style>
:root {
	--tileDim: 100px;
}

.color-tile {
	height: var(--tileDim);
	width: var(--tileDim);
	border: 2px solid;
}
</style>
