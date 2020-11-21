<template>
	<div class="body">
		<div class="header">
			<Graph v-for="(data, index) in dataInput" style="flex-grow: 1;"
				:Title="data['title']"
				:Location="data['location']"
				:StartDate="data['startDate']"
				:EndDate="data['endDate']"
				:ChartData="data['chartData']"
				:Width="data['width']"
				:Height="data['height']"
				@click="randomize(index)"
			/>
			<div class="map"></div>
		</div>
		<div class="lower">
			<div class="left">
				<SelectedStat
					Title="CORONA VIRUS (COVID-19)"
				/>
			</div>
			<div class="mainContent">
				<div class="sliderBar">
					<div class="playButton">
						<div class="playText">Play</div>
					</div>
					<div class="slider">
						<svg id="sliderSVG" height="40" width="900" :onmousemove="getMouseX" :onmouseleave="DragFalse">
							<line x1="30" x2="880" y1="20" y2="20" stroke="#60e4fa" stroke-width="3px" stroke-linecap="butt" />
							<circle id="sliderCircle" cx="30" cy="20" r="18" stroke="#60e4fa" stroke-width="3" fill="black" :onmousedown="DragTrue" :onmouseup="DragFalse" />
						</svg>
					</div>
					<div class="date">
						March 23
					</div>
				</div>
				<p> {{sliderValue}} </p>
			</div>
		</div>
	</div>
</template>

<script>
// imports:
import Graph from '../components/Graph.vue';
import SelectedStat from '../components/SelectedStat.vue';

export default{
	name: 'Graphs',
	data() {
		return {
			dataInput: [],
			drag: false,
			sliderValue: 0,
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
		},
		getMouseX(event) {
			if(!this.drag) return;
			var svg = document.getElementById('sliderSVG');
			var pt = svg.createSVGPoint();
	    pt.x = event.clientX;
			var circle = document.getElementById('sliderCircle');
	    let x = Math.floor(pt.matrixTransform(svg.getScreenCTM().inverse()).x);
	    x = (x < 30) ? 30 : x;
	    x = (x > 880) ? 880 : x;
	    if(this.sliderValue != Math.floor((x-30)/20)) this.sliderValue = Math.floor((x-30)/20); // lessens range of sliderValue, helps lessen updates to sliderValue when dragged
	    circle.setAttributeNS(null, "cx", x);
		},
		DragTrue(){
			this.drag = true;
		},
		DragFalse(){
			this.drag = false;
		},
	},
	beforeMount() {
		this.dataInput = [
			{
				title: "Existing Cases",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 175,
				height: 225,
			},
			{
				title: "Total Cases",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 175,
				height: 225,
			},
			{
				title: "New Cases",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 175,
				height: 225,
			},
			{
				title: "New Deaths",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 175,
				height: 225,
			},
			{
				title: "Deaths",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 175,
				height: 225,
			},
			{
				title: "Recovered",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 175,
				height: 225,
			},
			{
				title: "Death Rate",
				location: "World",
				startDate: "22 Jan",
				endDate: "Now",
				chartData: [6,8,4,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
				width: 175,
				height: 225,
			},
		];
	},
	components: {
		Graph,
		SelectedStat,
	}
}
</script>

<style scoped>
	.body{
		background-color: #080e1e;
		height: 89vh;
		padding: 15px;
	}
	.header{
		display: flex;
	}
	.map{
		border: 2px solid blue;
		flex-grow: 20;
		height: 225px;
		margin: 10px;
	}
	.lower {
		height: 63vh;
		margin: 10px;
		background-color: transparent;
		display: flex;
	}
	.left {
		width: 29%;
		height: 100%;
		margin-right: 10px;
		border: 2px solid orange;
	}
	.mainContent {
		height: 100%;
		width: 100%;
		/*border: 2px solid red;*/
	}
	.sliderBar {
		display: flex;
		width: 85%;
	}
	.sliderBar > * {
		color: #60e4fa;
		filter: brightness(0.8);
	}
	.playButton {
		width: 100px;
		border: 2px solid #60e4fa;
		border-radius: 5px;
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.playText {
		font-size: 1.3rem;
	}
	.slide {
		flex-grow: 1;
		margin: 10px;
	}
	.date {
		font-size: 2rem;
		width: 200px;
	}
</style>