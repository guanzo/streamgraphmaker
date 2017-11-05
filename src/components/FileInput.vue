
<template>
	<div class="file-input">
		<div>
			<label for="file">Upload a csv/tsv file</label>
			<input ref="fileinput" id="file" type="file" @change="loadFile" accept=".csv, .tsv, .txt" />
		</div>
		<div>
			<button @click="onSample">view sample file</button>
		</div>
	</div>
</template>

<script>

import * as d3 from 'd3'
import isNaN from 'lodash/isNaN'

export default {
	name:'file-input',
	data(){
		return {
			reader: new FileReader(),
			hasError: false
		}
	},
	methods:{
		loadFile(){
			this.hasError = false;

			var file = this.$refs.fileinput.files[0];      
			this.reader.addEventListener("load", this.parseFile, false);
			if (file) {
				this.reader.readAsText(file);
			}
		},
		parseFile(){
			var file = this.$refs.fileinput.files[0]
			var fileType = file.name.split('.').pop().toLowerCase();
			var data;

			if(fileType == 'csv'){
				data = d3.csvParse(this.reader.result,this.parseRow);
			}
			else if( fileType == 'tsv'){
				data = d3.tsvParse(this.reader.result,this.parseRow);
			}else
                this.hasError = true;

			if(!this.hasError)
				this.$emit('graphData',data)
			else
				this.$emit('error')
		},
		parseRow(d){
			Object.keys(d).forEach((key)=>{
				var value = d[key]
				var number;
				try{
					number = parseFloat(value.replace(/,/g,''))
					if(!isNaN(number))
						d[key] = number;
				}catch(e){
					console.log(e)
					this.hasError = true;
					number = value;
				}
			});
			return d
		},
		onSample(){
			d3.tsv('/sample-files/sample.tsv',this.parseRow,(error, data)=>{
				this.$emit('graphData',data)
			})
		}
	}
}

</script>

<style lang="scss" scoped>

.file-input{
	display: flex;
	align-items: center;
	justify-content: space-between;
}

</style>