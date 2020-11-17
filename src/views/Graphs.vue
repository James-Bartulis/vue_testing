<template>
		<g v-for="(data, index) in dataInput">
			<Graph 
				:Title="data['title']"
				:Location="data['location']"
				:StartDate="data['startDate']"
				:EndDate="data['endDate']"
				:ChartData="data['chartData']"
				:Width="data['width']"
				:Height="data['height']"
				@click.prevent="randomize(index)"
			/>
		</g>
</template>

<script>
// imports:
import Graph from '../components/Graph.vue';

export default{
	name: 'Graphs',
	data() {
		return {
			dataInput: [],
			title: "Existing Cases",
			location: "World",
			startDate: "22 Jan",
			endDate: "Now",
			chartData: [5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
			width: 250,
			height: 250,
		}
	},
	methods: {
		randomize(index) {
			this.dataInput[index]['chartData'] = [];
			let range = Math.floor(Math.random() * 5000000);
			for(let i = 0; i < 406; i++){
				this.dataInput[index]['chartData'].push(1 + Math.floor(Math.random() * range));
			}

			this.dataInput[index]['chartData'].sort(function(a, b){return a-b});
		}
	},
	computed: {},
	beforeMount() {
		this.dataInput = [
			{
				title: "Existing Cases",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 200,
				height: 250,
			},
			{
				title: "Total Cases",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 200,
				height: 250,
			},
			{
				title: "New Cases",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 200,
				height: 250,
			},
			{
				title: "New Deaths",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 200,
				height: 250,
			},
			{
				title: "Deaths",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 200,
				height: 250,
			},
			{
				title: "Recovered",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 200,
				height: 250,
			},
			{
				title: "Death Rate",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 200,
				height: 250,
			},
		];
	},
	mounted() {
	},
	components: {
		Graph,
	}
}
</script>

<style scoped>
	
</style>