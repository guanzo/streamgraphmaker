
<template>
	<div>
		<div class="svg-wrapper">
			<div ref="legend" class="legend"></div>
			<svg ref="svg" >
				<g class="margin-group">
					<g class="x axis"></g>	
					<g class="y axis"></g>
				</g>
			</svg>
		</div>
	</div>
</template>

<script>

import * as d3 from 'd3'
import _ from 'lodash'

export default {
	name:'streamgraph',
	props:['graphData','colorScale','margin', 'stackOrder','stackOffset'],
	data(){
		return {
			transitionDuration: 1000,
			keys:[]
		}
	},
	watch:{
		graphData(){
			this.doDataJoin();
		},
		colorScale(){
			this.doDataJoin();
		},
		margin:{
			handler(){
				this.doDataJoin()
			},
			deep: true
		},
		stackOrder(){
			this.doDataJoin()
		},
		stackOffset(){
			this.doDataJoin()
		}
	},
	methods:{
		doDataJoin(){
			var data = this.graphData;
			var margin = this.margin;

			var xAxisProp = data.columns[0]
			var keys = this.keys = data.columns.slice(1)
			
			var stack = d3.stack()
				.keys(keys)
				.order(this.stackOrder)
				.offset(this.stackOffset);

			var series = stack(data);

			var svg 	= d3.select(this.$refs.svg),
				bbox 	= svg.node().getBoundingClientRect(),
				width 	= bbox.width - margin.left - margin.right,
				height 	= bbox.height - margin.top - margin.bottom;

			svg = svg.select('.margin-group').attr("transform", "translate(" + margin.left + "," + margin.top + ")");

			var xScale = d3.scalePoint()
				.domain(data.map(d=>d[xAxisProp]))
				.range([0, width]);

			var yScale = d3.scaleLinear()
				.domain([d3.min(series, this.stackMin), d3.max(series, this.stackMax)])
				.range([height, 0]);

			var area = d3.area()
				.curve(d3.curveCardinal)
				.x((d, i)=>xScale(d.data[xAxisProp]))
				.y0(d=>yScale(d[0]))
				.y1(d=>yScale(d[1]));  
			
			svg.select('.x.axis')
				.attr("transform", "translate(0," + height + ")")
				.transition().duration(this.transitionDuration)
				.call(d3.axisBottom(xScale))

			svg.select('.y.axis')
				.transition().duration(this.transitionDuration)
				.call(d3.axisLeft(yScale))
				
			let streams = svg.selectAll('path.stream')
				.data(series,d=>d.key);
			streams = streams.enter()
					.append('path')
					.attr('class','stream')
				.merge(streams)
				.transition()
				.duration(this.transitionDuration)
					.attr('d',area)
					.attr('fill',(d,i)=>{
						let step = i/keys.length
						return this.colorScale(step)
					})

			this.createLegend(series)
		},
		createLegend(series){
			var legend = d3.select(this.$refs.legend)
			console.log(series)
			var legendUpdate = legend.selectAll('.legend-item')
					.data(series,d=>d.key);
			var legendEnter = legendUpdate.enter().append('div')
					.attr('class','legend-item')
			
			legendEnter.append('div')
				.attr('class','color-square')
			legendEnter.append('span')
						.text(d=>d.key)
			
			legendEnter.merge(legendUpdate)
				.select('.color-square')
				.transition()
				.duration(this.transitionDuration)
				.style('background',(d,i)=>{
					let step = (1 - i/this.keys.length)
					return this.colorScale(step)
				})
		},
		stackMax(layer) {
			return d3.max(layer,d=>d[1]);
		},
		stackMin(layer) {
			return d3.min(layer,d=>d[0]);
		}
	}
}

</script>

<style lang="scss">

$width: 960px;
$height: 500px;

.svg-wrapper{
	position: relative;
}

svg {
	border: 1px solid #eee;
	width: $width;
	height: $height;
}

.legend{
	position: absolute;
	top: 0;
	left: 100%;
	margin-left: 5px;
}
.legend-item{
	height:18px;
	margin-bottom:4px;
	display: flex;
	align-items: center;
}
.color-square{
	width:14px;
	height:14px;
	margin-right:5px;
}
</style>