<template>
  <div class="pathfinding">
  	<ul>
      <li class="buttons" v-for="(button, index) in buttons">
    		<button v-on:click="cycle(index)" :class="pickTool(button.state, index)">{{button.name}}</button>
      </li>
  	</ul>
    <ul>
      <li class="buttons"><button v-on:click="search();">Search</button></li>
      <li class="buttons"><button v-on:click="clearAll();">Clear All</button></li>
    </ul>
    <div class="grid" v-on:mouseup="this.mouseDown = false;if(this.buttons[5].state) search();">
      <ul v-for="y in height">
        <li v-for="x in width" v-on:mousedown="this.mouseDown = true; useToolHere((x-1), (y-1));" v-on:mouseenter="useToolHere((x-1), (y-1))" :class="['box', updateCell(hideDetail( grid[(y-1)*width+(x-1)].state ))]">{{ cellValue(this.grid[(y-1)*width+(x-1)].g) }}</li>
      </ul>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src

function sleep(ms) {
  return new Promise(resolve => setTimeout(resolve, ms));
}

class Node {
  constructor(x, y) {
    this.x = x;
    this.y = y;
    this.g = 999999;
    this.h = 999999;
    this.f = this.g + this.h;
    this.visited = false;
    this.prev = null;
    this.state = 0;
  }
};

class PriorityQueue { 
  constructor() 
  { 
      this.items = []; 
  } 
  enqueue(node) 
  {
    var contain = false;
    for (var i = 0; i < this.items.length; i++) {
      if (this.items[i].f > node.f) {
        this.items.splice(i, 0, node); 
        contain = true; 
        break; 
      } 
    }
    if (!contain) this.items.push(node);
  }
  dequeue() 
  {
    if (this.isEmpty()) return "Underflow";
    return this.items.shift(); 
  }
  isEmpty() { return this.items.length == 0; }
  clear() {while(!this.isEmpty()) this.dequeue();}
}
const BIGNUM = 999999;


export default {
  name: 'Pathfinding',
  data() {
    return {
    	buttons: [
      /*0*/  {name: "Details", state: true, range: 2},
      /*1*/  {name: "Tool", state: 0, range: 4}, // start, stop, wall, remove
      /*2*/  {name: "Heuristic", state: true, range: 2},
      /*3*/  {name: "Diagonals", state: true, range: 2},
      /*4*/  {name: "Animate", state: true, range: 2},
      /*5*/  {name: "Auto Search", state: true, range: 2},
      ],
      Searching: false,
      width: 53,
      height: 24,
      grid: [],
      ToolNames: [
        "Tool_Remove",
        "Tool_Start",
        "Tool_End",
        "Tool_Wall",
        "Tool_New",
        "Tool_Old",
        "Tool_Path",
      ],
      mouseDown: false,
      start: {
        x: -1,
        y: -1,
      },
      end: {
        x: -1,
        y: -1,
      },
      Q: null,
      steps: 0,
      resultSize: 0,
      scale: 1,
    }
  },
  methods: {
    cycle(index) {
      if(this.buttons[index].range == 2){                                   this.buttons[index].state = !this.buttons[index].state;
      }else if(this.buttons[index].state < this.buttons[index].range - 1){  this.buttons[index].state++;
      }else{ this.buttons[index].state = 0; }
      if(this.buttons[5].state && index > 1) this.search(); // intent: if Heuristic or Diagonals is changed, re-search
    },
    cellValue(value){
      if(this.buttons[0].state == true && value != BIGNUM) return value;
      return "";
    },
    pickTool(value, index) {
      let name = "";
      switch(value){
        case true: return "Tool_Start";
        case false: return "Tool_End";
        default: name = (value < 4) ? this.ToolNames[value] : "Tool_Failure";
        break;
      }
      return this.buttons[index].name = name;
    },
    useToolHere(x, y) {// refactor so you don't use buttons[2].value to access currently selected tool
      // keep track of last position of start & end
      // remove old and update new
      // if -1, -1 set new, else remove old and update new
      if(this.mouseDown){
        if((this.start.y == y && this.start.x == x) ||
            this.end.y == y && this.end.x == x) return;
        let toolValue = this.buttons[1].state;
        switch(toolValue){
          case 1: // start point
            if(!(this.start.x == -1 && this.start.y == -1)) this.grid[this.start.y * this.width + this.start.x].state = 0;
            this.start.x = x;
            this.start.y = y;
            break;
          case 2: // end point
            if(!(this.end.x == -1 && this.end.y == -1)) this.grid[this.end.y * this.width + this.end.x].state = 0;
            this.end.x = x;
            this.end.y = y;
            break;
          default: break;
        }
        this.grid[y * this.width + x].state = toolValue;
      }
    },
    updateCell(value) {
      return this.ToolNames[value];
    },
    hideDetail(state) {
      if(!this.buttons[0].state){
        switch(state) {
          case 4:
          case 5:
            return 0;
        }
      }
      return state;
    },
    async search() {
      if(this.start.x === -1 || this.start.y === -1 || this.end.x === -1 || this.end.y === -1) return;
      this.resetNodes();
      this.Q.clear();
      var cur;
      let x, y, startIndex = this.start.y * this.width + this.start.x;
      this.grid[startIndex].g = 0;
      this.grid[startIndex].h = this.Heuristic(this.grid[startIndex]);
      this.Q.enqueue(this.grid[startIndex]);
      this.Searching = true;
      this.steps = 0;
      this.scale = 1;
      while(!this.Q.isEmpty() && this.Searching){
        cur = this.Q.dequeue();
        x = cur.x;
        y = cur.y;
        if(x === this.end.x && y === this.end.y){ // found end node
          this.foundEnd(cur);
          return;
        }
        if(!(x === this.start.x && y === this.start.y)){ // if not start node
          this.grid[y * this.width + x].state = 5;
        }
        if(cur.visited) continue; // if visited, skip
        cur.visited = true;
        let addWeight;
        for(let i = -1; i < 2; i++){
          for(let j = -1; j < 2; j++){
            const index = (y + i) * this.width + x + j; 
            if(y + i < 0 || y + i >= this.height || x + j < 0 || x + j >= this.width) continue;
            if(this.grid[index].state === 3) continue;
            if(!this.buttons[3].state && (i + j + 2) % 2 === 0) continue;
            else if((i + j + 2) % 2 === 0) addWeight = 15;
            else if((i + j + 2) % 2 === 1) addWeight = 10;
            if(!this.grid[index].visited){
              if(this.grid[index].h >= BIGNUM) this.grid[index].h = this.Heuristic(this.grid[index]);
              if(this.grid[index].g > cur.g + addWeight){
                this.grid[index].g = cur.g + addWeight;
                this.grid[index].f = this.grid[index].g + this.grid[index].h;
                this.grid[index].prev = cur;
                if(this.grid[index].state !== 2){
                  this.grid[index].state = 4;
                  if(this.Searching && this.buttons[4].state && this.steps++ >= this.scale && this.buttons[0].state){
                    this.steps = 0;
                    this.scale += 0.5;
                    await sleep(0.0001);
                  }
                }else{
                  this.foundEnd(this.grid[index]);
                  return;
                }
                if(!this.grid[index].visited) this.Q.enqueue(this.grid[index]);
              }
            }else if(this.grid[index].state === 2){
              this.foundEnd(cur);
              return;
            }
          }
        }
      }
      this.Searching = false;
      return;
    },
    async foundEnd(cur) {
      let list = [];
      let index;
      list.push(cur);
      while(cur.prev != null){
        cur = cur.prev;
        list.push(cur);
      }
      this.resultSize = list.length;
      this.Searching = false;
      for(let i = list.length - 1; i >= 0; i--){
        if(!((list[i].x === this.start.x && list[i].y === this.start.y) || (list[i].x === this.end.x && list[i].y === this.end.y))){
          index = list[i].y * this.width + list[i].x;
          this.grid[index].state = 6;
          if(this.steps++ >= this.scale && this.buttons[4].state){
            if(this.Searching) return;
            this.steps = 0;
            await sleep(0.0001);
          }
        }
      }
      this.Searching = false;
      return;
    },
    Heuristic(node) {
      if(this.buttons[2].state){
        let minnn = Math.min(Math.abs(node.y - this.end.y), Math.abs(node.x - this.end.x)); 
        let maxxx = Math.max(Math.abs(node.y - this.end.y), Math.abs(node.x - this.end.x));
        return 10*(minnn * 1.5 + maxxx - minnn);
      }else
        return 0;
    },
    clearWalls() {
      this.grid.forEach(node => {
        if(node.state == 5){
          node.state = 0;
        }
      });
    },
    clearAll() {

      this.grid.forEach(node => {node.state = 0;});
      this.resetNodes();
      this.start.x = this.start.y = this.end.x = this.end.y = this.resultSize = -1;

    },
    resetNodes(){
      this.grid.forEach(node => {
        if(node.state == 4 || node.state == 5 || node.state == 6){
          node.state = 0;
          node.g = BIGNUM;
          node.h = BIGNUM;
          node.prev = null;
          node.visited = false;
          node.f = node.g + node.h;
        }else if(node.state > 5 || node.state < 6){
          node.g = BIGNUM;
          node.h = BIGNUM;
          node.prev = null;
          node.visited = false;
          node.f = node.g + node.h;
        }
      });
    }
  },
  beforeMount() {
    for(let i = 0; i < this.height; i++){
      for(let j = 0; j < this.width; j++){
        this.grid.push(new Node(j, i));
      }
    }
    this.Q = new PriorityQueue();
  }
}
</script>

<style scoped>
  * {
    margin: 0;
    padding: 0;
  }
  ul {
    list-style-type: none;
    display: flex;
  }
  li {
    font-size: .7em;
  }
  .buttons {
    flex-grow: 1;
  }
  button {
    border-radius: 0em;
    border: 0.2em solid #333;
    width: 100%;
    padding: 0.5em;
    font-size: 1em;
    font-weight: bold;
    outline: none;
  }
  .crossed {
    text-decoration: line-through;
  }
  .Tool_Start {
    background-color: #1FAB1F;
  }
  .Tool_End {
    background-color: #AB1F1F;
  }
  .Tool_Wall {
    background-color: black;
    color: white;
  }
  .Tool_Remove {
    background-color: white;
  }
  .Tool_New {
    background-color: #7BCDE8;
  }
  .Tool_Old {
    background-color: #8644C3;
  }
  .Tool_Path {
    background-color: blue;
  }
  .box {
    min-width: 3em;
    min-height: 3em;
    border: .1em solid black;
    cursor: pointer;
  }
  .grid{
    user-select: none;
    -webkit-user-drag:none;
  }

</style>
