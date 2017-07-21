<template>
	<div>
		<h5>Labels</h5>
		<div class="inputs">
			<div v-for="(value, key) in labels" :key="key">
				<label>{{ key }}</label>
				<input v-model="labels[key]">
			</div>
		</div>
	</div>
</template>

<script>

export default {
	name: 'labels',
	props:['margin', 'labels'],
	data(){
		return {
		}
	},
	watch:{
		labels:{
			handler(){
				this.$emit('labels',this.labels)
			},
			deep:true
		}
	}
}

</script>

<style lang="scss">

.inputs{
	display: flex;
	> div{
		margin-right: 15px;
	}
}

</style>