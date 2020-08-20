<template>
  <div class="main">

    <v-app-bar style="height: 80px;">
        <v-toolbar-title style="font-size: 40px; font-weight: bold; padding-top: 20px; color: #33cabb" >Sorting Visualiser</v-toolbar-title>
        

          <v-spacer></v-spacer>
          

              <v-slider style="padding-top: 50px;" 
                    label="Number" v-model="number"
                    :thumb-size="24" thumb-label="always" 
                    :disabled="isSorting"
                    min="10" max="50"
                    @change="generate">
              </v-slider>
              
              <v-slider style="padding-top: 50px;" 
                        label="Speed" v-model="speed"
                        :thumb-size="24" thumb-label="always" 
                        tick-size= "20"
                        min="1" max="12">
              </v-slider>

          

          <div class="button-container">

              <v-btn class="button white--text" color="#33bdef"
                      v-on:click="shuffle" 
                      :disabled="isSorting" >Shuffle!</v-btn>
              <v-btn class="button white--text" color="#33bdef"
                      v-on:click="sort(selectedAlgo)" 
                      :disabled="isSorting">Sort!</v-btn>
                      
          </div>
          
          
            <v-select :items="sortAlgorithms" 
                      item-text="name"
                      item-value="id"
                      v-model="selectedAlgo"
                      :disabled="isSorting"
                      label="Algorithm" outlined
                      style="padding-top: 50px;" right>
            </v-select>
          
        </v-app-bar>
    

    <div class="hello">

        <transition-group tag="div" name="list-animation" class="array-to-be-sorted">

           <div v-for="num in array" v-bind:key="num" 
                    v-bind:class="['list-animation-item', 'list-item' ]"
                        v-bind:style="{ height: 6.0*num + 'px' }">
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
      var n = this.number, i;
      var a =[];
      for(i = 1; i <= n; i++) {
        a.push(i*100/n);
      }
      this.array = a;
  },
  methods: {
      generate: function() {
        this.array = [];
        var n = this.number;
        for(var i = 1; i <= n; i++) {
          this.array.push(i*100/n);
        }
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

input:focus,
select:focus,
textarea:focus,
button:focus {
    outline: none;
}
.main{
    width:1265px; /* The width is fixed by pixels */
    height:800px;
    background-color: #1fed89;
    background-image: linear-gradient(160deg, #1fed89 0%, #12d6e7 50%, #341bdf 100%);

;
}
.hello {
  margin: 10px;
  overflow: hidden;
  position: relative;
}

.array-to-be-sorted {
    display: flex;
    position: relative;
    justify-content: center;
    align-items: center;
}

.list-item {
    display: flex;
    position: relative;
    justify-items: center;
    align-items: center;
    background-color: #e903d6;
    padding: 1px;
    width: 100%;
    max-width: 30px;
    border-radius: 25px;
    color: white;
    height:50%;
    margin: 2px;
    bottom: -290px;
    
   
   
    

    &.active {
      background: #92cc6b;
    }
    &.swaping {
      background: #c5fcd5;
    }
    &.traversing {
      background: #ad6bcc;
    }
}

.list-animation-item {
    transition: all ease 0.5s;
}
.button-container {
  margin-top: 30px;
  padding: 10px;
}
.button {
  margin: 5px 15px;
}

.button:hover {
    background-color: #33cabb;
}

.button:active {
    position: relative;
    top: 1px;
}
    
</style>
