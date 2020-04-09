<template>
<div class="home">
    <div v-if="notBig" class="home">


      <div v-if="searchByTitle" class="home">
        <div class="form">
          <div class="cont">
            <h1>Search By Title</h1>
            <button @click="searchByTypeButton">By Type</button>
            <button @click="searchByMakerButton">By Creator</button>
          </div>
          <input v-model="findTitle" placeholder="Search Title">
          <div class="pokemon">
            <div v-if="suggestionsTitle.length > 0">
              <div v-for="s in suggestionsTitle" :key="s.id" @click="selectItem(s)">
                <h2>{{s.title}}</h2>
                <div></div>
                <img :src="s.path" />
                <div class="space"></div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="searchByType" class="home">
        <div class="form">
          <div class="cont">
            <h1>Search By Type</h1>
            <button @click="searchByTitleButton">By Title</button>
            <button @click="searchByMakerButton">By Creator</button>
          </div>
          <v-select :options="['Drinks', 'Appetizers', 'Dressing', 'Bread', 'Breakfast', 'Salad', 'Soup', 'Pasta', 'Rice', 'Vegetables', 'Chicken', 'Ham', 'Hamburger', 'Beef', 'Sausage/ Pork', 'Tuna/ Seafood', 'Crockpot/ Instant pot', 'Dessert']" v-model="findType"></v-select>
          <div v-if="suggestionsType.length > 0">
            <div v-for="s in suggestionsType" :key="s.id" @click="selectItem(s)">
              <h2>{{s.typeFood}}:  {{s.title}}</h2>
              <img :src="s.path" />
              <div class="space"></div>
            </div>
          </div>
        </div>
      </div>

      <div v-if="searchByMaker" class="home">
        <div class="form">
          <div class="cont">
            <h1>Search By Creator</h1>
            <button @click="searchByTitleButton">By Title</button>
            <button @click="searchByTypeButton">By Type</button>
          </div>
          <input v-model="findMaker" placeholder="Search Maker">
          <div v-if="suggestionsMaker.length > 0">
            <div v-for="s in suggestionsMaker" :key="s.id" @click="selectItem(s)">
              <h2>{{s.maker}}:  {{s.title}}</h2>
              <img :src="s.path" />
              <div class="space"></div>
            </div>
          </div>
        </div>
      </div>


      <div class="form" v-if="findItem">
        <div class="inputBoxes">
          <h1>{{findItem.title}}</h1>
          <h4>{{findItem.maker}}  ({{findItem.typeFood}})</h4>
          <img :src="findItem.path" />

        <div class="heading">
        <h3>Ingredients</h3>
        </div>
        <div class="add">
        <div class="form2">
            <div id="instruction_box" v-for="index in findItem.numIngredients" class="inputBoxes" v-bind:key="index">
            <div class="sidebox1">
                <div class="amount">
                <p>{{findItem.amounts[(index - 1)]}}</p>
                </div>
                <p>{{findItem.ingredients[(index - 1)]}}</p>
            </div>
            </div>
        </div>
        </div>

        <div class="heading">
        <h3>Directions</h3>
        </div>
        <div class="add">
        <div class="form2">
            <div id="instruction_box" v-for="index in findItem.numInstructions" class="inputBoxes" v-bind:key="index">
            <div class="sidebox2">
                <p>{{numbers[index]}}.  {{findItem.instructions[(index - 1)]}}</p>
            </div>
            </div>
        </div>
        </div>
        </div>
        <p></p>
        <button @click="makeBigger">Make Bigger</button>
        
      </div>
    </div>
    <div v-else>
      <div class="form3">
        <div class="inputBoxes">
          <h1>{{findItem.title}}</h1>
          <h4>{{findItem.maker}}</h4>
          <img :src="findItem.path" />

        <div class="heading">
        <h3>Ingredients</h3>
        </div>
        <div class="add">
        <div class="form2">
            <div id="instruction_box" v-for="index in findItem.numIngredients" class="inputBoxes" v-bind:key="index">
            <div class="sidebox1">
                <div class="amount">
                <p>{{findItem.amounts[(index - 1)]}}</p>
                </div>
                <p>{{findItem.ingredients[(index - 1)]}}</p>
            </div>
            </div>
        </div>
        </div>

        <div class="heading">
        <h3>Directions</h3>
        </div>
        <div class="add">
        <div class="form2">
            <div id="instruction_box" v-for="index in findItem.numInstructions" class="inputBoxes" v-bind:key="index">
            <div class="sidebox2">
                <p>{{numbers[index]}}.  {{findItem.instructions[(index - 1)]}}</p>
            </div>
            </div>
        </div>
        </div>
        </div>
        <p></p>
      <button @click="makeSmaller">Go Back</button>
      </div>
      
    </div>
</div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios';
import 'vue-select/dist/vue-select.css';
export default {
  name: 'Home',
  data() {
    return {
     items: [],
     findTitle: "",
     findType: "",
     findMaker: "",
     findItem: null,
     numbers: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15],
     notBig: true,
     searchByTitle: true,
     searchByType: false,
     searchByMaker: false,
    }
  },
  created() {
    this.getItems();
  },
  methods: {
    async getItems() {
        let response = await axios.get("/api/items");
        this.items = response.data;
        return true;
    },
    selectItem(item) {
      this.findTitle = "";
      this.findItem = item;
    },
    makeBigger() {
      this.notBig = false;
    },
    makeSmaller() {
      this.notBig = true;
    },
    searchByTitleButton() {
      this.searchByTitle = true;
      this.searchByType = false;
      this.searchByMaker = false;
    },
    searchByTypeButton() {
      this.searchByTitle = false;
      this.searchByType = true;
      this.searchByMaker = false;
    },
    searchByMakerButton() {
      this.searchByTitle = false;
      this.searchByType = false;
      this.searchByMaker = true;
    },
  },
  computed: {
    suggestionsTitle() {
      let items = this.items.filter(item => item.title.toLowerCase().startsWith(this.findTitle.toLowerCase()));
      return items.sort((a, b) => a.title > b.title);
    },
    suggestionsType() {
      let items = this.items.filter(item => item.typeFood.toLowerCase().startsWith(this.findType.toLowerCase()));
      return items.sort((a, b) => a.typeFood > b.typeFood);
    },
    suggestionsMaker() {
      let items = this.items.filter(item => item.maker.toLowerCase().startsWith(this.findMaker.toLowerCase()));
      return items.sort((a, b) => a.maker > b.maker);
    },
  },
}
</script>



<style>
.pokemon {
  width: 100%;
  
}

.suggestions {
  width: 100%;
 
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.cont {
  display: flex;
  align-items: center;
  width: 100%;
}

.cont button {
  height: 25px;
}

.cont h1 {
  padding-right: 13px;
}

.form3 {
  width: 900px;
  border: 1px solid black;
  display: flex;
  flex-direction: column;
  padding: 30px;
}

.form3 img {
  width: 60%;
}

.upload {
  border: 1px solid black;
}

.form2 {
  width: 100%;
  margin-left: 10px;
  margin-right: 10px;
}

.inputBoxes {
  width: 100%;
}

.sidebox1 {
  width: 100%;
  display: flex;
  height: 35px;
}

.space {
  padding: 20px;
}

.home {
  display: flex;
  justify-content: center;
  width: 100%;
}

.form h2 {
  font-size: 25px;
}

.form {
  width: 45%;
  
}

.home img {
  width: 100%;
}

.image h2 {
  font-style: italic;
}

/* Masonry */
*,
*:before,
*:after {
  box-sizing: inherit;
}

.image-gallery {
  column-gap: 1.5em;
}

.image {
  margin: 0 0 1.5em;
  display: inline-block;
  width: 100%;
}

.image img {
  width: 100%;
}

/* Masonry on large screens */
@media only screen and (min-width: 1024px) {
  .image-gallery {
    column-count: 4;
  }
}

/* Masonry on medium-sized screens */
@media only screen and (max-width: 1023px) and (min-width: 768px) {
  .image-gallery {
    column-count: 3;
  }
}

/* Masonry on small screens */
@media only screen and (max-width: 767px) and (min-width: 540px) {
  .image-gallery {
    column-count: 2;
  }
}
</style>
