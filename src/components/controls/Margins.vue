<template>
	<div>
		<h5>Margins</h5>
		<div class="inputs">
			<div v-for="(value,position) in margin">
				<label>{{ position }}</label>
				<input type="number" v-model.number="margin[position]">
			</div>
		</div>
	</div>
</template>

<script>

export default {
	name: 'margin-control',
	props:['margin'],
	data(){
		return {
			margins:['top','right','bottom','left']
		}
	}
}

</script>

<style lang="scss" scoped>

.inputs{
	display: flex;
	> div{
		margin-right: 15px;
	}
	input {
		width: 65px;
	}
}

</style>