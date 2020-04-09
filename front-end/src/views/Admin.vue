<template>
<div class="admin">
  <h1>Add A Recipe!</h1>
    <div class="heading">
      <div class="circle">1</div>
      <h2>Enter the Recipe name, who created it, and picture</h2>
    </div>
    <div class="add">
      <div class="form">
        <div class="inputBoxes">
          <input v-model="title" placeholder="Recipe name">
          <input v-model="maker" placeholder="Created by">
        </div>
        <p></p>
        <input type="file" name="photo" @change="fileChanged">
      </div>
    </div>

    <div class="heading">
      <div class="circle">2</div>
      <h2>Ingredients</h2>
    </div>
    <div class="add">
      <div class="form">
        <div id="instruction_box" v-for="index in numIngredients" class="inputBoxes" v-bind:key="index">
          <div class="sidebox">
            <div class="amount">
            <input v-model='amounts[(index - 1)]' placeholder='Amount'>
            </div>
            <input v-model='ingredients[(index - 1)]' placeholder='Ingredient'>
          </div>
        </div>
        
        <div class="inputBoxes">
          <div class="sideButtons">
            <button v-on:click="addLineIngredient">Add</button>
            <button @click="removeLineIngredient">Remove</button>
          </div>
        </div>
        <p></p>
      </div>
    </div>

    <div class="heading">
      <div class="circle">3</div>
      <h2>Directions</h2>
    </div>
    <div class="add">
      <div class="form">
        <div id="instruction_box" v-for="index in numInstructions" class="inputBoxes" v-bind:key="index">
          <div class="sidebox">
            <textarea v-model='instructions[(index - 1)]' placeholder= 'Direction'></textarea>
          </div>
        </div>
        <div class="inputBoxes">
          <div class="sideButtons">
            <button @click="addLineInstruction">Add</button>
            <button @click="removeLineInstruction">Remove</button>
          </div>
        </div>
        <p></p>
      </div>
    </div>


    <div class="heading">
      <div class="circle">4</div>
      <h2>Type of Food</h2>
    </div>
    <div class="add">
      <div class="form">
        <div id="instruction_box" class="inputBoxes">
          <v-select :options="['Drinks', 'Appetizers', 'Dressing', 'Bread', 'Breakfast', 'Salad', 'Soup', 'Pasta', 'Rice', 'Vegetables', 'Chicken', 'Ham', 'Hamburger', 'Beef', 'Sausage/ Pork', 'Tuna/ Seafood', 'Crockpot/ Instant pot', 'Dessert']" v-model="typeFood"></v-select>
        </div>
        <p></p>
      </div>
    </div>

    <div class="heading">
      <div class="circle">5</div>
      <h2>Double check the information and Upload your Recipe</h2>
    </div>
    <div class="add">
      <div class="form">
        <div id="instruction_box" class="inputBoxes">
          <button @click="upload">Upload</button>
          <div id="done"></div>
        </div>
        <p></p>
      </div>
    </div>
</div>
</template>

<style>
.amount {
  width: 100px;
}

.sidebox {
  display: flex;
}

/* Suggestions */
.suggestions {
  width: 200px;
  border: 1px solid #ccc;
}

.suggestion {
  min-height: 20px;
}

.suggestion:hover {
  background-color: #5BDEFF;
  color: #fff;
}

.image h2 {
  font-style: italic;
  font-size: 1em;
}

.heading {
  display: flex;
  margin-bottom: 20px;
  margin-top: 20px;
}

.heading h2 {
  margin-top: 8px;
  margin-left: 10px;
}

.add,
.edit {
  display: flex;
}

.circle {
  border-radius: 50%;
  width: 18px;
  height: 18px;
  padding: 8px;
  background: #333;
  color: #fff;
  text-align: center
}

/* Form */
input,
textarea,
select,
button {
  font-family: 'Montserrat', sans-serif;
  font-size: 1em;
}

.form {
  margin-right: 50px;
}

/* Uploaded images */
.upload h2 {
  margin: 0px;
}

.upload img {
  max-width: 300px;
}

.inputBoxes {
  display: flex;
  flex-direction: column;
}
</style>


<script>
import axios from 'axios';
import 'vue-select/dist/vue-select.css';
export default {
  name: 'Admin',
  data() {
    return {
      title: "",
      maker: "",
      instructions: [],
      amounts: [],
      ingredients: [],
      file: null,
      addItem: null,
      items: [],
      findTitle: "",
      findItem: null,
      numIngredients: 5,
      numInstructions: 3,
      typeFood: "",
    }
  },
  created() {
    this.getItems();
  },
  methods: {
    fileChanged(event) {
      this.file = event.target.files[0]
    },
    async upload() {
        const formData = new FormData();
        formData.append('photo', this.file, this.file.name)
        let r1 = await axios.post('/api/photos', formData);
        let r2 = await axios.post('/api/items', {
          title: this.title,
          maker: this.maker,
          instructions: this.instructions,
          amounts: this.amounts,
          ingredients: this.ingredients,
          typeFood: this.typeFood,
          numIngredients: this.numIngredients,
          numInstructions: this.numInstructions,
          path: r1.data.path
        });
        this.addItem = r2.data;
        this.title = "";
        this.maker = "";
        this.instructions = [];
        this.amounts = [];
        this.ingredients = [];
        this.typeFood = "";
        this.path = "";
        this.numIngredients = 5;
        this.numInstructions = 3;
        document.getElementById("done").innerHTML = "<h2>Your recipe has been added!</h2>"
    },
    async getItems() {
        let response = await axios.get("/api/items");
        this.items = response.data;
        return true;
    },
    selectItem(item) {
      this.findTitle = "";
      this.findItem = item;
    },
    async deleteItem(item) {
        await axios.delete("/api/items/" + item._id);
        this.findItem = null;
        this.getItems();
        return true;
    },
    async editItem(item) {
        await axios.put("/api/items/" + item._id, {
          title: this.findItem.title,
          instructions: this.findItem.instructions
        });
        this.findItem = null;
        this.getItems();
        return true;
    },
    addLineIngredient() {
      this.numIngredients++;
    },
    removeLineIngredient() {
      this.numIngredients--;
      if (this.numIngredients < 0){
        this.numIngredients = 0;
      }
    },
    addLineInstruction() {
      this.numInstructions++;
    },
    removeLineInstruction() {
      this.numInstructions--;
      if (this.numInstructions < 0){
        this.numInstructions = 0;
      }
    }
  },
  computed: {
    suggestions() {
      let items = this.items.filter(item => item.title.toLowerCase().startsWith(this.findTitle.toLowerCase()));
      return items.sort((a, b) => a.title > b.title);
    },
  },
}
</script>
