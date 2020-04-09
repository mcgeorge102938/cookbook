<template>
<div class="admin">
    <div class="heading">
      <h1>Edit/Delete an Item</h1>
    </div>
    <div class="edit">
      <div class="form">
        <h4>Select a Recipe</h4>
        <h4>Edit what needs to be changed and pressed the edit button</h4>
        <h4>To erase a recipe completely press delete button</h4>
        <input v-model="findTitle" placeholder="Search">
        <div class="suggestions" v-if="suggestions.length > 0">
          <div class="suggestion" v-for="s in suggestions" :key="s.id" @click="selectItem(s)">{{s.title}}
          </div>
        </div>
      </div>
      <div class="upload2" v-if="findItem">
        <div class="inputBoxes">
          <input v-model="findItem.title">
          <input v-model="findItem.maker">

        <div class="heading">
        <h2>Ingredients</h2>
        </div>
        <div class="add">
        <div class="form">
            <div id="instruction_box" v-for="index in findItem.numIngredients" class="inputBoxes" v-bind:key="index">
            <div class="sidebox">
                <div class="amount">
                <input v-model='findItem.amounts[(index - 1)]' placeholder='Amount'>
                </div>
                <input v-model='findItem.ingredients[(index - 1)]' placeholder='Ingredient'>
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
        <h2>Directions</h2>
        </div>
        <div class="add">
        <div class="form">
            <div id="instruction_box" v-for="index in findItem.numInstructions" class="inputBoxes" v-bind:key="index">
            <div class="sidebox">
                <textarea v-model='findItem.instructions[(index - 1)]' placeholder= 'Direction'></textarea>
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
            <h2>Type of Food</h2>
            </div>
            <div class="add">
            <div class="form">
                <div id="instruction_box" class="inputBoxes">
                <v-select :options="['Drinks', 'Appetizers', 'Dressing', 'Bread', 'Breakfast', 'Salad', 'Soup', 'Pasta', 'Rice', 'Vegetables', 'Chicken', 'Ham', 'Hamburger', 'Beef', 'Sausage/ Pork', 'Tuna/ Seafood', 'Crockpot/ Instant pot', 'Dessert']" v-model="findItem.typeFood"></v-select>
                </div>
                <p></p>
            </div>
        </div>

        </div>
        <p></p>
        <img :src="findItem.path" />
      </div>
      <div class="actions" v-if="findItem">
        <button @click="deleteItem(findItem)">Delete</button>
        <button @click="editItem(findItem)">Edit</button>
      </div>
    </div>
</div>
</template>

<style>
.upload2 {
    width: 60%;
}

.upload2 img {
    width: 80%;
}

.admin h4 {
    font-size: 12px;
}

.admin h1 {
    font-size: 30px;
}

.amount {
  width: 100px;
}

.space {
    padding: 15px;
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
          instructions: this.findItem.instructions,
          maker: this.findItem.maker,
          amounts: this.findItem.amounts,
          ingredients: this.findItem.ingredients,
          typeFood: this.findItem.typeFood,
          numInstructions: this.findItem.numInstructions,
          numIngredients: this.findItem.numIngredients,
        });
        this.findItem = null;
        this.getItems();
        return true;
    },
    addLineIngredient() {
      this.findItem.numIngredients++;
    },
    removeLineIngredient() {
      this.findItem.numIngredients--;
      if (this.findItem.numIngredients < 0){
        this.findItem.numIngredients = 0;
      }
    },
    addLineInstruction() {
      this.findItem.numInstructions++;
    },
    removeLineInstruction() {
      this.findItem.numInstructions--;
      if (this.findItem.numInstructions < 0){
        this.findItem.numInstructions = 0;
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
