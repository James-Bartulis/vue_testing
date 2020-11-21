<template>
  <div class="leftContainer">
    <div>
      <p style="float:left;margin: 0px 0px 10px 15px;font-size:25px;">{{Title}}</p>
      <svg :width="Width" :height="Height">
        Selected STAT
      </svg>
    </div>
    <div class="stats">
      
    </div>
    <div class="rankedBoard">
      <!-- Title -->
      <p style="float:left; font-size: 20px;margin:0;">NEW CASES (Top World)</p>
      <svg width="400" style="border: none;">
        <!-- Line -->
        <line x1="0" x2="400" y1="0" y2="0" stroke="#60e4fa" stroke-width="2" stroke-linecap="butt" />
        <!-- List -->
        <g v-for="(place, index) in Places" :transform="`translate(100, ${(index+1)*15})`" >
          <!-- Bar -->
          <polygon :points="makeRankedBar(place.value)" stroke-linecap="butt" fill="#de3539"/>
          <!-- Name -->
          <text fill="#60e4fa" x="10">{{index+1}}. {{place.name}}</text>
          <!-- Value -->
          <text fill="#60e4fa" x="200">{{place.value}}</text>  
        </g>
      </svg>
    </div>
  </div>
</template>

<script>
export default {
  name: 'SelectedStat',
  props: {
    Title: {
      default: "",
      type: String
    },
    Width: {
      default: 400,
      type: Number
    },
    Height: {
      default: 150,
      type: Number
    },
    Places: {
      default: [
        {name: "Spain", value: 4321},
        {name: "USA", value: 1529},
        {name: "Iran", value: 1411},
        {name: "Germany", value: 1347},
        {name: "Switzerland", value: 1073},
        {name: "France", value: 671},
        {name: "Portugal", value: 460},
        {name: "Belgium", value: 342},
      ],
      type: Array
    },
  },
  data() {
    return {
      maxValue: 0,
      // Debug stuff
    }
  },
  methods: {
    makeRankedBar(value) {
      return "0,0 " + -Math.floor((value/this.maxValue * 100)) + ",0 " + -Math.floor((value/this.maxValue * 100)) + ",-10 0,-10 0,0";
    }
  },
  computed: {
    setMaxValue(){
      let max = this.Places[0].value;
      this.Places.forEach( place => {
        max = place.value > max ? place.value : max;
      });
      this.maxValue = max;
      return this.maxValue;
    },
  },
  beforeUpdate() {
    this.setMaxValue;
  },
  beforeMount() {
    this.setMaxValue;
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  svg {
    border: 2px solid blue;
  }
  .leftContainer {
    color: #60e4fa;
    filter: brightness(0.8);
    background-color: #1A2032;
    padding: 10px;
    box-sizing: border-box;
    height: 100%; /*can definitely code the height a more dynamic way*/
    border: 2px solid pink;
  }
  .leftContainer > * {
    margin-top: 10px;
  }
  .stats {
    border: 2px solid green;
    height: 40%;
  }
  .rankedBoard {
    /*border: 2px solid red;*/
  }
/*  svg {
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
    border: 7px solid red;
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
  }*/
</style>
