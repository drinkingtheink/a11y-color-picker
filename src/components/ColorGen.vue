<template>
  <div class="color-generator">
    <h3>Check out generated colors:</h3>

		<section class="color-output">
			<Tile v-for="color in countArr" :key="color" :a11yThresh="a11yThresh" :comparisonColor="color">
			</Tile>
		</section>
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
		color: {
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
    }
  },
	mounted() {
		for(let i = 0; i < this.count; i++){
			this.countArr.push(`color-space-${i}`);
		}
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
