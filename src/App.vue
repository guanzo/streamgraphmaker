<template>
	<div class="container">
		<h1>Stream Graph Maker</h1>
		<file-input @graphData="onGraphData" @error="onError"></file-input>
		<streamgraph v-if="!hasError" 
			:graphData="graphData" 
			:colorScale="colorScale" 
			:margin="margin" 
			:stackOrder="stackOrder"
			:stackOffset="stackOffset"
			:hasError="hasError"
		></streamgraph>
		<error v-else ></error>
		<br>
		<div v-if="!hasError && graphData.length">
			<h3>Customize</h3>
			<margins :margin="margin"></margins>
			<hr>
			<stack-layout @stackOrder="onStackOrder" @stackOffset="onStackOffset"></stack-layout>
			<hr>
			<color-picker @colorScale="onColorScale"></color-picker>
		</div>
	</div>
</template>

<script>

import FileInput from './components/FileInput.vue'
import Streamgraph from './components/Streamgraph.vue'
import Margins from './components/controls/Margins.vue'
import StackLayout from './components/controls/StackLayout.vue'
import ColorPicker from './components/controls/ColorPicker.vue'
import Error from './components/Error.vue'
import * as d3 from 'd3'
import * as chromatic from 'd3-scale-chromatic'

export default {
	name: 'app',
	data () {
		return {
			graphData:[],
			colorScale: chromatic.interpolateBlues,
			hasError: false,
			margin: { top: 20, right: 20, bottom: 40, left: 60 },
			stackOrder: d3.stackOrderNone,
			stackOffset: d3.stackOffsetNone
		}
	},
	methods:{
		onGraphData(graphData){
			this.hasError = false;
			this.$nextTick(()=> this.graphData = graphData )
		},
		onColorScale(colorScale){
			this.colorScale = colorScale;
		},
		onStackOrder(stackOrder){
			this.stackOrder = stackOrder
		},
		onStackOffset(stackOffset){
			this.stackOffset = stackOffset
		},
		onError(){
			this.hasError = true;
		}
	},
	components:{
		Streamgraph,
		FileInput,
		ColorPicker,
		Margins,
		StackLayout,
		Error
	}
}
</script>

<style lang="scss">

* {
	box-sizing: border-box;
}

</style>
