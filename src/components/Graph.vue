<template>
  <svg :width="Width" :height="Height" v-on:mouseenter="HoverOn()" v-on:mouseleave="HoverOff()" :class="{'brighter' : isHovered}">
    <!-- Titles -->
    <text x="50%" y="10%" dominant-baseline="middle" text-anchor="middle" fill="#60e4fa" class="dimmer medium" :class="{'brighter' : isHovered}"> {{Title}} </text>
    <text x="50%" y="19%" dominant-baseline="middle" text-anchor="middle" fill="#375466" class="dimmer small" :class="{'brighter' : isHovered}"> {{Location}} </text>
    <g class="dimmer large" :class="{'brighter' : isHovered}">
      <text x="25%" y="30%" dominant-baseline="middle" text-anchor="middle" fill="#60e4fa">{{formatNumbers(lastValue)}}</text>
      <text x="70%" y="30%" dominant-baseline="middle" text-anchor="middle" fill="#60e4fa">({{formatNumbers(recentChange, true)}})</text>
    </g>

    <!-- Bottom Titles -->
    <text :x="dataWidth-3" :y="bottomGraph + 10" class="dimmer" :class="{'brighter' : isHovered}" fill="#60e4fa">{{EndDate}}</text>
    <text x="10" :y="bottomGraph + 10" class="dimmer" :class="{'brighter' : isHovered}" fill="#60e4fa"> {{StartDate}} </text>

    <!-- Horizontal Lines -->
    <g v-for="n in horizontalLines" :transform="`translate(10, ${bottomGraph})`" class="dimmer supersmall" :class="{'brighter' : isHovered}">
      <line x1="0" :x2="graphWidth" :y1="-(n-1)*adjustedHeight(maxValue)/2" :y2="-(n-1)*adjustedHeight(maxValue)/2" stroke="#375466" stroke-width="1" stroke-linecap="butt"/>
      <text :x="graphWidth + 5" :y="-(n-1)*adjustedHeight(maxValue)/2 + 5">{{formatNumbers((n-1) * maxValue/2)}}</text>
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
export default {
  name: 'Graph',
  props: {
    Title: {
      default: "",
      type: String
    },
    Location: {
      default: "",
      type: String
    },
    StartDate: {
      default: "",
      type: String
    },
    EndDate: {
      default: "",
      type: String
    },
    ChartData: {
      required: true,
      default: [0,1,2,3,4,5,6,7,8,9,10,11,12,13,14],
      type: Array
    },
    Width: {
      default: 250,
      type: Number
    },
    Height: {
      default: 250,
      type: Number
    },
  },
  data() {
    return {
      lastValue: 0,
      recentChange: 0,
      maxValue: 1,
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
    adjustedHeight(value) {
      return (this.dataHeight - this.bottomGap) / this.maxValue * value;
    },
    setSimplifiedData() {
      this.simplifiedData = [];
      let average = 0;
      let section = 7;
      for(let i = 0; i < this.ChartData.length; i++){
        average += this.ChartData[i];
        if((i+1)%section == 0){
          this.simplifiedData.push(average/section);// maybe add Math.floor()
          average = 0;
        }
      }
    },
    createPolygonGraphData(){
      let x = 0, result = "", last = "";
      this.barWidth = this.dataWidth / this.simplifiedData.length;
      result = "";
      result += this.dataWidth + ",0";
      result += " 0,0";
      this.simplifiedData.forEach(value => {
        last = "";
        last += ' ' + x + ',' + String(-this.adjustedHeight(value));
        x += this.barWidth;
        last += ' ' + x + ',' + String(-this.adjustedHeight(value));
        result += last;
      });
      last += ' ' + x + ",0 " + String(x-this.barWidth) + ",0";
      this.polygonGraphData = result;
      this.lastDataPoint = last;
    },
    HoverOn() {
      this.isHovered = true;
    },
    HoverOff() {
      this.isHovered = false;
    },
    formatNumbers(value, WSign = false) {
      let rounded, negative = false;
      if(value < 0){
        value *= -1;
        negative = true;
      }
      if(value > 1000000){
        rounded = String((value/1000000).toFixed(1));
        if(rounded[rounded.length-1] == '0') { rounded = rounded.slice(0, rounded.length-2); }
        return (negative ? '-' : (WSign ? '+' : '')) + rounded + 'm';

      }else if(value > 1000){
        rounded = String((value/1000).toFixed(1));
        if(rounded[rounded.length-1] == '0') { rounded = rounded.slice(0, rounded.length-2); }
        return (negative ? '-' : (WSign ? '+' : '')) + rounded + 'k';

      }else{
        rounded = String((value).toFixed(1));
        if(rounded[rounded.length-1] == '0') { rounded = rounded.slice(0, rounded.length-2); }
        return (negative ? '-' : (WSign ? '+' : '')) + rounded;
      }
    }
  },
  computed: {
    setMaxValue(){
      let max = this.simplifiedData[0];
      this.simplifiedData.forEach( value => {
        max = value > max ? value : max;
      });
      this.maxValue = max;
      return this.maxValue;
    },
  },
  beforeUpdate(){
    this.setSimplifiedData();
    if(this.simplifiedData.length > 1){
      this.lastValue = this.simplifiedData[this.simplifiedData.length - 1];
      this.recentChange = this.lastValue - this.simplifiedData[this.simplifiedData.length - 2];
    }
    this.setMaxValue;
    this.createPolygonGraphData();
  },
  beforeMount() {
    this.graphHeight = this.Height * 0.45;
    this.graphWidth = this.Width * 0.73;
    this.dataHeight = this.graphHeight * 0.8;
    this.dataWidth = this.graphWidth * 0.85;
    this.bottomGraph = this.Height - this.bottomGap;

    this.setSimplifiedData();
    this.setMaxValue;
    this.lastValue =this.simplifiedData[this.simplifiedData.length - 1];
    this.recentChange = this.lastValue -this.simplifiedData[this.simplifiedData.length - 2];
    this.createPolygonGraphData();

    // Debug Lines
    this.dataBounds = "10," + String(this.Height - this.dataHeight) + " " + String(this.dataWidth + 10) + "," + String(this.Height - this.dataHeight) + " " + String(this.dataWidth + 10) + "," + String(this.Height - this.bottomGap);
    this.graphBounds = "10," + String(this.Height - this.graphHeight) + " " + String(this.graphWidth + 10) + "," + String(this.Height - this.graphHeight) + " " + String(this.graphWidth + 10) + "," + String(this.Height - this.bottomGap);
    this.bottomGapBounds = "10," + String(this.Height - this.bottomGap) + " " + String(this.graphWidth + 10) + "," + String(this.Height - this.bottomGap) + " " + String(this.graphWidth + 10) + "," + String(this.Height - this.bottomGap);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  svg {
    margin: 10px;
    background-color: #1A2032;
    font-size: 0.7em;
    user-select: none;
    border: 3px solid #60e4fa;
    border-radius: 5px;
    position: relative;
  }
  svg:hover {
    margin: 0px;
    padding: 6px;
    border: 7px solid #de3539;
    border-radius: 5px;
    top: -10px;
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
