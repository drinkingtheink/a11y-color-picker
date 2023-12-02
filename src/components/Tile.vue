<template>
  <div class="color-tile" :class="{ 'isA11y': isA11y }" :style="`background-color: ${myColor}`" @click="handleTileClick(myColor, contrast)">
		<p>{{ myColor }}</p>
		<p>{{ contrast }}</p>
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
		reColor: {
			type: Boolean,
		},
		userMinThresh: {
			type: Number,
		},
  },
	data() {
		return {
			myColor: null,
		}
	},
	watch: {
    myColor() {
      if (this.contrast && this.contrast < this.a11yThresh) {
				setTimeout(() => {
					this.getColor();
				}, 100);
			}
    },
		comparisonColor() {
			this.getColor();
		},
		reColor() {
			if (this.reColor) this.getColor();
		},
  },
	methods: {
		getColor() {
			this.myColor = chroma.random();
		},
		handleTileClick(color) {
			this.$emit('colorSelected', color, this.contrast);
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
		},
		isA11y() {
			if (this.contrast) {
				return this.contrast >= this.a11yThresh;
			}

			return false;
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
	transition: all 0.2s;
}

.color-tile:hover {
	cursor: pointer;
	transform: scale(1.2);
}

.isA11y {
	color: white;
}
</style>
