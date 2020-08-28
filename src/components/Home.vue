<template>
  <div class="main">

    <v-app-bar color="white">
        <v-toolbar-title  style="font-size: 20px; " color="pink"><strong>Sorting Algo Visualiser</strong> </v-toolbar-title>
        
          <v-spacer></v-spacer>
          
          <v-row style="padding-top: 30px;">
            <v-col cols="md">
              <v-slider
                    color="black"
                    label="Number" v-model="number"
                    :thumb-size="19" thumb-label="always" 
                    :disabled="isSorting"
                    min="20" max="50"
                    @change="generate">
              </v-slider>
              </v-col>
              <v-col cols="md">
              <v-slider
                        color="black"
                        label="Speed" v-model="speed"
                        :thumb-size="19" thumb-label="always" 
                        tick-size= "20"
                        min="1" max="10">
              </v-slider></v-col>
          </v-row>
          

          <div class="button-container">

              <v-btn small  rounded  class="button white--text" color="black"
                      v-on:click="shuffle" 
                      :disabled="isSorting" >Shuffle!</v-btn>
              <v-btn small  rounded  class="button white--text" color="black"
                      v-on:click="sort(selectedAlgo)" 
                      :disabled="isSorting">Sort!</v-btn>
                      
          </div>
          

            <v-select :items="sortAlgorithms" 
                      item-text="name"
                      item-value="id"
                      v-model="selectedAlgo"
                      :disabled="isSorting"
                      label="Algorithm" outlined
                      style="padding-top: 30px; width: 0px" right>
            </v-select>
          
        </v-app-bar>
    

    <div class="hello">

        <transition-group tag="div" name="list-animation" class="array-to-be-sorted">

            <div v-for="num in array" v-bind:key="num" 
                    v-bind:class="['list-animation-item', 'list-item' ]"
                        v-bind:style="{ height: 4.8*num*100/number + 'px' }">
            </div>
            
        </transition-group>
       
    </div>
    
  </div>
</template>


<script>
export default {
  name: 'Home',

  data() {
    return {
        a: [],
        array: [],
        isManipulatingArray: false,

        selectedAlgo: "1",

        number: "20",
        speed: "5",

        isSorting: false,

        styleObject: {
          height: "200px",
        },
        sortAlgorithms: [
            { id: '1', name: "Bubble Sort", fn: "bubbleSort" },
            { id: '2', name: "Selection Sort", fn: "selectionSort" },
            { id: '3', name: "Insertion Sort", fn: "insertionSort" },
        ],
    }
  },
  created() {
      var n = this.number;
      for(let i = 1; i <= 50; i++) {
        this.a.push(i);
      }
      this.array = this.a.slice(0, n);
  },
  methods: {
      generate: function() {
        var n = this.number;
        this.array = [];
        this.array = this.a.slice(0, n);
      },
      
      sort: function (algo) {
        this.isSorting = true;
        if(algo=="1") this.bubbleSort(this.array);
        else if(algo=="2") this.selectionSort(this.array);
        else if(algo=="3") this.insertionSort(this.array);
      },

      sleep: function() {
        return new Promise(resolve => setTimeout(resolve, (100*5)/this.speed));
      },

      arraySetWithoutIndexes: async function(array, index, value) {
          array.splice(index, 1, value);
      },

      arraySwap: async function(array, indexA, indexB) {
        var x = array[indexA];
        this.arraySetWithoutIndexes(array, indexA, array[indexB]);
        this.arraySetWithoutIndexes(array, indexB, x);
      },

      shuffle: function() {
        this.isSorting = false;
        var a = this.array;
        var j, i;
        for (i = a.length - 1; i > 0; i--) {
            j = Math.floor(Math.random() * (i + 1));
            this.arraySwap(a, i, j);
        }
      },

      bubbleSort: async function(a) {
        var len = a.length;
        for (var i = len - 1; i >= 0; i--) {
            for (var j = 1; j <= i; j++) {
                if (a[j - 1] > a[j]) {
                    this.arraySwap(a, j - 1, j);
                    await this.sleep();
                }
            }
        }
        this.isSorting = false;
      },

      selectionSort: async function(a) {
        var minIdx, len = a.length;

        for (var i = 0; i < len; i++) {
            minIdx = i;
            for (var j = i + 1; j < len; j++) {
                if (a[j] < a[minIdx]) {
                    minIdx = j;
                }
            }
            this.arraySwap(a, i, minIdx);
            await this.sleep();
        }
        this.isSorting = false;
      },

      insertionSort: async function(a) {
        var i, len = a.length, el, j;

        for (i = 1; i < len; i++) {
            el = a[i];
            j = i;

            while (j > 0 && a[j - 1] > el) {
                a[j] = a[j - 1];
                j--;
            }
            this.arraySetWithoutIndexes(a, j, el);
            await this.sleep();
        }
        this.isSorting = false;
      },
  }
}
</script>

<style lang="scss" scoped>


.main{
    width:100%;
    height: 100%;
    background-color: black;
    
}

.hello {
  margin: 10px;
}

.array-to-be-sorted {
    display: flex;
    position: relative;
    justify-content: center;
}

.list-item {
    display: flex;
    position: relative;
    justify-items: center;
    align-items: center;
    
    background: white;
    

    padding: 1px;
    width: 90%;
    max-width: 25px;
    border-radius: 0px 0px 10px 10px;
    color: white;
    margin: 2.5px;

    &.active {
      background: #92cc6b;
    }
    &.swaping {
      background: #cc6b6b;
    }
    &.traversing {
      background: #ad6bcc;
    }
}

.list-animation-item {
    transition: all ease 0.5s;
}
.button-container {
  padding: 10px;
}
.button {
  margin: 5px 15px;
}

.button:hover {
    background-color: black;
}

.button:active {
    position: relative;
    top: 1px;
}
    
</style>