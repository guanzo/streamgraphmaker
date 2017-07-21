<template>
	<div>
		<h5>Stack Layout</h5>
		<div class="flex">
			<div>
				Stack order
				<div v-for="order in orders" :key="order.name">
					<label>
						<input type="radio" name="order" v-model="stackOrder" :value="order.name">
						{{ order.short + " - " + order.desc }}
					</label>
				</div>
			</div>
			<div>
				Stack offset
				<div v-for="offset in offsets" :key="offset.name">
					<label>
						<input type="radio" name="offset" v-model="stackOffset"  :value="offset.name">
						{{ offset.short + " - " + offset.desc }}
					</label>
				</div>
			</div>
		</div>
	</div>
</template>

<script>

import * as d3 from 'd3'

export default {
	name: 'stack-layout',
	props:['margin'],
	data(){
		return {
			stackOrder: 'stackOrderNone',
			stackOffset: 'stackOffsetNone',
			orders: [
				{ name: 'stackOrderNone', 		short:'None', desc: 'use the given series order' },
				{ name: 'stackOrderReverse', 	short:'Reverse', desc: 'use the reverse of the given series order' },
				{ name: 'stackOrderAscending', 	short:'Ascending', desc: 'put the smallest series on bottom' },
				{ name: 'stackOrderDescending', short:'Descending', desc: 'put the largest  series on bottom' },
				{ name: 'stackOrderInsideOut', 	short:'Inside Out', desc: 'put larger series in the middle' },
			],
			offsets: [
				{ name: 'stackOffsetNone',		short:'None', desc: 'apply a zero baseline' },
				{ name: 'stackOffsetExpand', 	short:'Expand', desc: 'normalize the baseline to zero and topline to one' },
				{ name: 'stackOffsetDiverging', short:'Diverging', desc: 'positive above zero; negative below zero' },
				{ name: 'stackOffsetSilhouette',short:'Silhouette', desc: 'center the streamgraph around zero' },
				{ name: 'stackOffsetWiggle', 	short:'Wiggle', desc: 'minimize streamgraph wiggling' },

			]
		}
	},
	watch:{
		stackOrder(){
			this.$emit('stackOrder', d3[this.stackOrder])
		},
		stackOffset(){
			this.$emit('stackOffset', d3[this.stackOffset])
		}
	},
}

</script>

<style lang="scss" scoped>

.flex{
	display: flex;
	flex-wrap: wrap;
	justify-content: space-between;
	> div{
		padding: 5px;
	}
}

</style>