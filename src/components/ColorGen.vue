<template>
  <div class="color-generator">
    <h3>Check out generated colors:</h3>

		<section class="color-output">
			<Tile 
				v-for="color in countArr" 
					:key="color" 
					:a11yThresh="a11yThresh" 
					:comparisonColor="selectedColor" 
					:reColor="reColor"
					@colorSelected="handleTileSelection"
				>
			</Tile>
		</section>

		<button @click="reColor = true">Redo Colors</button>
  </div>
</template>

<script>
import Tile from './Tile.vue';

export default {
  name: 'ColorGen',
  props: {
    count: {
			type: Number,
			default: 10,
		},
		selectedColor: {
			type: String,
		},
  },
	components: {
    Tile
  },
  data() {
    return {
			a11yThresh: 4.4,
			countArr: [],
			reColor: false,
    }
  },
	mounted() {
		for(let i = 0; i < this.count; i++){
			this.countArr.push(`color-space-${i}`);
		}
	},
	watch: {
    reColor() {
			setTimeout(() => {
				this.reColor = false;
			}, 100);
    },
		comparisonColor() {
			this.getColor();
		},
  },
	methods: {
		handleTileSelection(color, contrast) {
			this.$emit('setSecondary', color, contrast);
		},
	}
}
</script>

<style>
.color-output {
	display: flex;
	justify-content: center;
	max-width: 80vw;
	margin: 0 auto;
}
</style>
