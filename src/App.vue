<template>
	<div class="container">
		<h1>Stream Graph Maker</h1>
		<file-input @graphData="onGraphData" @error="onError"></file-input>
		<br>
		<streamgraph v-if="!hasError" 
			:graphData="graphData" 
			:colorScale="colorScale" 
			:labels="labels"
			:margin="margin" 
			:stackOrder="stackOrder"
			:stackOffset="stackOffset"
			:hasError="hasError"
		></streamgraph>
		<error v-else ></error>
		<br>
		<div v-if="!hasError && graphData.length">
			<h3>Customize</h3>
			<labels :labels="labels"></labels>
			<hr>
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
import Labels from './components/controls/Labels.vue'
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
			margin: { top: 30, right: 20, bottom: 60, left: 60 },
			labels:{
				title:'',
				['y axis']:'',
				['x axis']:''
			},
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
		Labels,
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
