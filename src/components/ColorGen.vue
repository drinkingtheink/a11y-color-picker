<template>
  <div class="color-generator">
    <h3>Check out generated colors:</h3>

		<section class="color-output">
			<Tile v-for="color in countArr" :key="color">
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

</style>
