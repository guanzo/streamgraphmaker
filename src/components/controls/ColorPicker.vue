
<template>
	<div>
		<h5>Color</h5>
		<div class="color-sample" v-for="(colorName,i) in colorBrewer" :key="colorName">
			<label>
				<input type="radio" name="color" :value="colorName" v-model="selectedColor">
				{{ colorName }}
			</label>
			 <div ref="colorBar" :style="createColorSample(colorName,i)" class="color-bar"></div> 
		</div>
	</div>
</template>

<script>

import * as d3 from 'd3'
import * as chromatic from 'd3-scale-chromatic'
import _ from 'lodash'

export default {
	name:'color-picker',
	data(){
		return {
			selectedColor: 'Blues',
			samples: 20,
			colorBrewer:['Blues','BrBG','BuGn','BuPu','GnBu','Greens','Greys','Oranges','OrRd','PiYG','PRGn','PuBu','PuBuGn','PuOr','PuRd','Purples','RdBu','RdGy','RdPu','RdYlBu','RdYlGn','Reds','Spectral','YlGn','YlGnBu','YlOrBr','YlOrRd']
		}
	},
	computed:{

	},
	watch:{
		selectedColor(){
			this.$emit('colorScale',chromatic[`interpolate${this.selectedColor}`])
		}
	},
	methods:{
		createColorSample(colorName,i){
			let colorScale = chromatic[`interpolate${colorName}`]

			var gradients = [];

			for(let i=0; i <= this.samples; i++){
				let step = i/this.samples;
				let color = colorScale(step)
				let percent = step*100;
				gradients.push(`${color} ${percent}%`)
			} 
			return {
				background: `linear-gradient(to right, ${gradients.join(',')} )`
			}
		}
	}
}

</script>

<style lang="scss" scoped>

.color-sample{
	display: flex;
	margin-bottom: 10px;
	label{
		margin-right: 10px;
		flex: 0 0 100px;
	}
	.color-bar{
		flex: 1;
	}
}

</style>