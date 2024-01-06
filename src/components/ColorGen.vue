<template>
  <div class="color-generator">
    <h3 class="options-header">Overlay options based on your set minimum contrast:</h3>

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

		<button @click="redoColors">Redo Colors</button>
		<button @click="showHelpModal">Help</button>
  </div>
</template>

<script>
import Tile from './Tile.vue';

const tileCount = 6;

export default {
  name: 'ColorGen',
  props: {
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
    cuePollForTiles: {
      type: Boolean,
    },
  },
	components: {
    Tile,
  },
  data() {
    return {
			countArr: [],
			reColor: false,
      seconds: 0,
      settledTiles: [],
      pollingForTiles: false,
    }
  },
	mounted() {
		for(let i = 0; i < tileCount; i++){
      const newEntry = {
        name: `color-space-${i}`,
      }
			this.countArr.push(newEntry);
		}

    this.pollForSettledTiles();
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
    selectedColor() {
      this.settledTiles = [];
      this.pollForSettledTiles();
    },
    cuePollForTiles() {
      if (this.cuePollForTiles) {
        this.settledTiles = [];
        this.pollForSettledTiles();
      }
    },
    seconds() {
      if (this.seconds === 10) {
        this.showHelpModal();
      }
    }
  },
	methods: {
    redoColors() {
      this.reColor = true;
      this.settledTiles = [];
      this.pollForSettledTiles();
    },
		handleTileSelection(color, contrast) {
			this.$emit('setOverlay', color, contrast);
		},
		showHelpModal() {
			this.$emit('showHelpModal');
		},
    settleTile(tileColor, tileIndex) {
      this.settledTiles.push({
        tile: tileIndex,
        color: tileColor,
      })
    },
    incrementSecondsAndPoll() {
      this.seconds++;

      this.pollForSettledTiles();
    },
    pollForSettledTiles() {
      this.pollingForTiles = true;

      if (this.settledTiles && this.settledTiles.length === tileCount) {
        this.pollingForTiles = false;
        this.seconds = 0;
      } else if (this.settledTiles && this.settledTiles.length < tileCount) {
        setTimeout(this.incrementSecondsAndPoll, 1000);
      }
    },
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
