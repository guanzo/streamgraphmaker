
<template>
	<div class="tip">
        <div v-if="hasError" class="error">There was an error with the uploaded file.</div>
		<div>
			The file format should follow the below table.<br>
			The x axis is derived from the first column,<br>
            Its value can be numeric/categorical.<br>
			All subsequent category values should be numeric.
		</div>
		<table>
			<thead>
				<tr>
					<th>Year</th>
					<th>Category 1</th>
					<th>Category 2</th>
					<th>Category 3</th>
				</tr>
			</thead>
			<tbody>
				<tr>
					<td>2000</td>
					<td>20</td>
					<td>50</td>
					<td>15</td>
				</tr>
				<tr>
					<td>2001</td>
					<td>90</td>
					<td>57</td>
					<td>84</td>
				</tr>
				<tr>
					<td>2002</td>
					<td>3</td>
					<td>60</td>
					<td>35</td>
				</tr>
			</tbody>

		</table>
	</div>
</template>

<script>

import * as d3 from 'd3'
import _ from 'lodash'

export default {
	name:'error',
    props:['hasError']
}

</script>

<style lang="scss" scoped>

$width: 960px;
$height: 500px;

.tip {
	border: 1px solid #eee;
	width: $width;
	height: $height;
	padding: 20px;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	text-align: center;
	> div.error{
        font-weight: bold;
        color: #E53935;
        margin-bottom: 20px;
	}
}

</style>