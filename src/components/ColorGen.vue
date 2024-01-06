<template>
  <div class="color-generator">
    <h3 class="options-header">Generated Overlay Color Options:</h3>

		<section class="color-output">
			<div class="annotation">
				<span class="hex">Hex Value <i class="arrow right"></i></span>
				<span class="cont">Contrast <i class="arrow right"></i></span>
			</div>
			<Tile 
				v-for="color,index in countArr" 
				:key="color" 
				:a11yThresh="a11yThresh" 
				:comparisonColor="selectedColor" 
				:reColor="reColor"
				:userMinThresh="userMinThresh"
				:lightOrDark="lightOrDark"
        :index="index"
				@colorSelected="handleTileSelection"
        @settleTile="settleTile"
			>
			</Tile>
		</section>

		<button @click="reColor = true">Redo Colors</button>
		<button @click="showHelpModal">Help</button>
  </div>
</template>

<script>
import Tile from './Tile.vue';
import chroma from "chroma-js";

export default {
  name: 'ColorGen',
  props: {
    count: {
      type: Number,
      default: 6,
    },
    selectedColor: {
      type: String,
    },
    a11yThresh: {
      type: Number,
    },
    userMinThresh: {
      type: Number,
    },
    lightOrDark: {
      type: Function,
    },
  },
	components: {
    Tile
  },
  data() {
    return {
			countArr: [],
			reColor: false,
      seconds: 0,
    }
  },
	mounted() {
		for(let i = 0; i < this.count; i++){
      const newEntry = {
        name: `color-space-${i}`,
        color: null,
        settled: false,
      }
			this.countArr.push(newEntry);
		}
	},
	watch: {
    reColor() {
      this.countArr.forEach((col) => {
        col.settled = false;
        col.color = null;
      })

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
			this.$emit('setOverlay', color, contrast);
		},
		showHelpModal() {
			this.$emit('showHelpModal');
		},
    settleTile(tileColor, tileIndex) {
      this.countArr[tileIndex].settled = true;
      this.countArr[tileIndex].color = chroma(tileColor).hex();
    }
	},
}
</script>

<style>
.color-output {
	display: flex;
	justify-content: center;
	max-width: 730px;
	margin: 0 auto 1rem auto;
	position: relative;
}

.color-output .annotation {
	position: absolute;
	width: 100px;
	text-align: right;
	left: -3rem;
	top: 0;
	height: 100px;
}

.color-output .hex,
.color-output .cont {
	position: absolute;
	right: 0;
	width: 100%;
	color: #111;
	display: block;
	padding: 5px 10px;
	background-color: #eaeaea;
	transform: scale(0.8);
	border-radius: 10px 0 0 10px;
    margin-right: -2px;
	box-shadow: 0 5px 5px 0px rgba(0,0,0,0.5);
}

.color-output .hex {
	top: 22px;
}

.color-output .cont {
	bottom: 0;
}
 
.arrow {
  border: solid #111;
  border-width: 0 3px 3px 0;
  display: inline-block;
  padding: 3px;
}

.right {
  transform: rotate(-45deg);
  -webkit-transform: rotate(-45deg);
}

.color-generator button {
	margin-right: 10px;
}
</style>
