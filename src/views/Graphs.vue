<template>
	<svg :width="width" :height="height" v-on:click="randomize" v-on:mouseenter="ToggleHover()" v-on:mouseleave="ToggleHover()" :class="{'brighter' : isHovered}">
		<!-- Titles -->
		<text x="50%" y="10%" dominant-baseline="middle" text-anchor="middle" fill="#60e4fa" class="dimmer medium" :class="{'brighter' : isHovered}"> {{title}} </text>
		<text x="50%" y="19%" dominant-baseline="middle" text-anchor="middle" fill="#375466" class="dimmer small" :class="{'brighter' : isHovered}"> {{location}} </text>
		<text x="50%" y="30%" dominant-baseline="middle" text-anchor="middle" fill="#60e4fa" class="dimmer large" :class="{'brighter' : isHovered}"> {{Math.floor(lastValue/1000)}}k ( +10.7k )</text>

		<!-- Bottom Titles -->
		<text :x="dataWidth-3" :y="bottomGraph + 10" class="dimmer" :class="{'brighter' : isHovered}" fill="#60e4fa">Now</text>
		<text x="10" :y="bottomGraph + 10" class="dimmer" :class="{'brighter' : isHovered}" fill="#60e4fa"> {{startDate}} </text>

		<!-- Horizontal Lines -->
		<g v-for="n in horizontalLines" :transform="`translate(10, ${bottomGraph})`" class="dimmer supersmall" :class="{'brighter' : isHovered}">
			<line x1="0" :x2="graphWidth" :y1="-(n-1)*adjustedHeight(maxValue)/2" :y2="-(n-1)*adjustedHeight(maxValue)/2" stroke="#375466" stroke-width="1" stroke-linecap="butt"/>
			<text :x="graphWidth + 5" :y="-(n-1)*adjustedHeight(maxValue)/2 + 5">{{ Math.floor(((n-1) * maxValue/2)/1000) }}k</text>
		</g>
		<!-- Data: -->
		<polygon :points="polygonGraphData" style="fill:#60e4fa; stroke:#60e4fa; stroke-width:1;" class="dimmer" :class="{'brighter' : isHovered}" :transform="`translate(10, ${bottomGraph})`" />
		<polygon :points="lastDataPoint" class="white" :class="{'red' : isHovered}" stroke-linecap="butt" :transform="`translate(10, ${bottomGraph})`" />

		<!-- Debug Lines -->
		<!-- <polyline :points="dataBounds" style="fill:transparent; stroke: red; stroke-width:3;"/> -->
		<!-- <polyline :points="graphBounds" style="fill:transparent; stroke: green; stroke-width:3;"/> -->
		<!-- <polyline :points="bottomGapBounds" style="fill:transparent; stroke: blue; stroke-width:3;"/> -->
	</svg>
</template>

<script>
// imports:

export default{
	name: 'Graphs',
	data() {
		return {
			title: "Existing Cases",
			location: "World",
			lastValue: 0,
			startDate: "22 Jan",
			chartData: [5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1,5,7,3,7,2,8,1,9,5,4,7,2,8,1],
			maxValue: 1,
			width: 250,
			height: 250,
			graphHeight: 0,
			graphWidth: 0,
			dataHeight: 0,
			dataWidth: 0,
			bottomGap: 20,
			bottomGraph: 0,
			simplifiedData: [],
			polygonGraphData: "",
			barWidth: 0,
			horizontalLines: 4,
			lastDataPoint: "",
			isHovered: false,

			// Debug stuff
			dataBounds: "",
			graphBounds: "",
			bottomGapBounds: "",
		}
	},
	methods: {
		randomize() {
			this.chartData = [];
			for(let i = 0; i < 203; i++){
				this.chartData.push(1 + Math.floor(Math.random() * 500000));
			}

			this.chartData.sort(function(a, b){return a-b});
			this.setSimplifiedData();
			this.setMaxValue;
			this.createPolygonGraphData();

			this.lastValue = Math.floor(this.simplifiedData[this.simplifiedData.length - 1]);
		},
		adjustedHeight(value) {
			return (this.dataHeight - this.bottomGap) / this.maxValue * value;
		},
		setSimplifiedData() {
			this.simplifiedData = [];
			let average = 0;
			let section = 7;
			for(let i = 0; i < this.chartData.length; i++){
				average += this.chartData[i];
				if((i+1)%section == 0){
					this.simplifiedData.push(average/section);// maybe add Math.floor()
					average = 0;
				}
			}
		},
		createPolygonGraphData(){
			let x = 0;
			this.barWidth = this.dataWidth / this.simplifiedData.length;
			this.polygonGraphData = "";
			this.polygonGraphData += this.dataWidth + ",0";
			this.polygonGraphData += " 0,0";
			this.simplifiedData.forEach(value => {
				this.lastDataPoint = "";
				this.lastDataPoint += ' ' + x + ',' + String(-this.adjustedHeight(value));
				x += this.barWidth;
				this.lastDataPoint += ' ' + x + ',' + String(-this.adjustedHeight(value));
				this.polygonGraphData += this.lastDataPoint;
			});
			this.lastDataPoint += ' ' + x + ",0 " + String(x-this.barWidth) + ",0";
		},
		ToggleHover() {
			this.isHovered = !this.isHovered;
		}
	},
	computed: {
		setMaxValue(){
			this.maxValue = this.simplifiedData[0];
			this.simplifiedData.forEach( value => {
				this.maxValue = value > this.maxValue ? value : this.maxValue;
			});
			return this.maxValue;
		},
	},
	beforeMount() {
		this.graphHeight = this.height * 0.45;
		this.graphWidth = this.width * 0.8;
		this.dataHeight = this.graphHeight * 0.8;
		this.dataWidth = this.graphWidth * 0.85;
		this.bottomGraph = this.height - this.bottomGap;

		this.chartData.sort(function(a, b){return a-b});
		this.setSimplifiedData();
		this.setMaxValue;
		this.createPolygonGraphData();

		// Debug Lines
		this.dataBounds = "10," + String(this.height - this.dataHeight) + " " + String(this.dataWidth + 10) + "," + String(this.height - this.dataHeight) + " " + String(this.dataWidth + 10) + "," + String(this.height - this.bottomGap);
		this.graphBounds = "10," + String(this.height - this.graphHeight) + " " + String(this.graphWidth + 10) + "," + String(this.height - this.graphHeight) + " " + String(this.graphWidth + 10) + "," + String(this.height - this.bottomGap);
		this.bottomGapBounds = "10," + String(this.height - this.bottomGap) + " " + String(this.graphWidth + 10) + "," + String(this.height - this.bottomGap) + " " + String(this.graphWidth + 10) + "," + String(this.height - this.bottomGap);
	}
}
</script>

<style scoped>
	svg {
		margin: 10px;
		background-color: #1A2032;
		font-size: 0.7em;
		user-select: none;
		border: 3px solid #60e4fa;
		border-radius: 5px;
	}
	svg:hover {
		border: 3px solid red;
		border-radius: 5px;
	}
	.dimmer {
		stroke-opacity: 0.7;
		fill-opacity: 0.7;
	}
	.brighter {
		fill-opacity: 1;
		stroke-opacity: 1;
	}
	.supersmall{
		font-size: .75rem;
		fill: #60e4fa;
	}
	.small{
		font-size: 1rem;
	}
	.medium{
		font-size: 1.2rem;
	}
	.large{
		font-weight: bold;
		font-size: 1.5rem;
	}
	.white {
		fill: white;
		stroke: white;
		stroke-width: 1;
	}
	.red {
		fill: red;
		stroke: red;
	}
</style>