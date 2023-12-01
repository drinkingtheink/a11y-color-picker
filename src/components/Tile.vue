<template>
  <div class="color-tile" :style="`background-color: ${myColor}`">
		<span>{{ myColor }}</span>
		<span>{{ contrast }}</span>
  </div>
</template>

<script>
import chroma from "chroma-js";

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
			this.myColor = chroma.random();
		}
	},
	mounted() {
		this.getColor();
	},
	computed: {
		contrast() {
			let contrast = null;

			if (this.myColor && this.comparisonColor) {
				contrast = chroma.contrast(this.myColor, this.comparisonColor).toFixed(1);
			}

			return contrast;
		}
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
	margin: 5px 0 0 5px;
}
</style>
