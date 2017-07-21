
<template>
	<div class="streamgraph">
		<div v-show="labels.title.length" class="title">{{ labels.title }}</div>
		<div ref="legend" class="legend"></div>
		<svg ref="svg" >
			<g class="margin-group">
				<g class="x axis"></g>	
				<g class="y axis"></g>
			</g>
		</svg>
	</div>
</template>

<script>

import * as d3 from 'd3'
import _ from 'lodash'

export default {
	name:'streamgraph',
	props:['graphData','colorScale','labels','margin', 'stackOrder','stackOffset'],
	data(){
		return {
			transitionDuration: 1000,
			keys:[],
			xScale: null,
			yScale: null
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
		},
		labels:{
			handler(){
				this.doDataJoin()
			},
			deep: true
		},
	},
	methods:{
		doDataJoin(){
			var data = this.graphData;
			var margin = this.margin;

			var svg 	= d3.select(this.$refs.svg),
				bbox 	= svg.node().getBoundingClientRect(),
				width 	= bbox.width - margin.left - margin.right,
				height 	= bbox.height - margin.top - margin.bottom;

			svg = svg.select('.margin-group').attr("transform", "translate(" + margin.left + "," + margin.top + ")");

			var xAxisProp = data.columns[0]
			var keys = this.keys = data.columns.slice(1)
			
			var stack = d3.stack()
				.keys(keys)
				.order(this.stackOrder)
				.offset(this.stackOffset);

			var series = stack(data);

			this.setScales(svg,data,series,width,height,xAxisProp);

			var area = d3.area()
				.curve(d3.curveCardinal)
				.x((d, i)=>this.xScale(d.data[xAxisProp]))
				.y0(d=>this.yScale(d[0]))
				.y1(d=>this.yScale(d[1])); 

			let streams = svg.selectAll('path.stream')
				.data(series,d=>d.key);

			streams.exit()
				.transition()
				.duration(this.transitionDuration)
				.style('opacity',0)
				.remove();

			streams = streams.enter()
					.append('path')
					.attr('class','stream')
					.style('opacity',0)
				.merge(streams)
				.transition()
				.duration(this.transitionDuration)
					.attr('d',area)
					.attr('fill',(d,i)=>{
						let step = i/keys.length
						return this.colorScale(step)
					})
					.style('opacity',1)

			this.setLegend(series)
		},
		setScales(svg, data, series, width, height, xAxisProp){
			
			this.xScale = d3.scalePoint()
				.domain(data.map(d=>d[xAxisProp]))
				.range([0, width]);

			this.yScale = d3.scaleLinear()
				.domain([d3.min(series, this.stackMin), d3.max(series, this.stackMax)])
				.range([height, 0]);
 
			
			svg.select('.x.axis')
				.attr("transform", "translate(0," + height + ")")
				.transition().duration(this.transitionDuration)
				.call(d3.axisBottom(this.xScale))

			let xLabel = svg.select('.x.axis').selectAll('.label')
				.data([this.labels['x axis']]);

			xLabel = xLabel.enter().append('text')
				.merge(xLabel)
				.attr('class','label')
				.attr("transform", "translate("+ (width/2) +",0)")
                .attr('rotate','45deg')
				.attr('y',30)
				.text(d=>d) 

			svg.select('.y.axis')
				.transition().duration(this.transitionDuration)
				.call(d3.axisLeft(this.yScale))
			
			let yLabel = svg.select('.y.axis').selectAll('.label')
				.data([this.labels['y axis']]);

			yLabel = yLabel.enter().append('text')
				.merge(yLabel)
				.attr('class','label')
				.attr('text-anchor','middle')
				.attr('y',-10) 
				.text(d=>d) 
		},
		setLegend(series){
			var legend = d3.select(this.$refs.legend)

			var legendUpdate = legend.selectAll('.legend-item')
					.data(series,d=>d.key);

			legendUpdate.exit().remove()

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

.streamgraph{
	position: relative;
	width: $width;
	height: $height;
	.title{
		font-size: 1.2em;
		position: absolute;
		left: 50%;
		top: 5px;
		transform: translateX(-50%);
	}
}

svg {
	border: 1px solid #eee;
	width: $width;
	height: $height;
}

.axis {
    &.x.tilted .tick text{
        transform: rotate(-45deg);
        text-anchor: end;    
    }
    .label{
        fill: #333;
        font-size: 14px;
        text-anchor: middle;
    }
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
    flex: none;
	margin-right:5px;
}
</style>