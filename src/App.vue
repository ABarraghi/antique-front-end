<template>
  <section>
    <div>
      <input type="text" placeholder="Search for an Antique" id="search-input">
      <button id="search-btn" @click="searchName()">Search!</button>
      <button id="clear-btn" @click="clearFilters()">Clear!</button>

    <div v-for="(val, key) in sorts">
      <input type="radio" name="radio" :value="val" v-model="selected" :id="key" @click="orderPrice(key)">
      <label :for="val">{{ val }}</label>
    </div>
    </div>
  </section>
  <section>
    <div>
      <table>
          <thead>
            <tr>
              <th>Id</th>
              <th>Name</th>
              <th>Description</th>
              <th>Origin</th>
              <th>Century</th>
              <th>Price</th>
              <th>Category</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(antique, index) in antiques" :key="index">
              <td>{{ antique.id }}</td>
              <td>{{ antique.name }}</td>
              <td>{{ antique.description }}</td>
              <td>{{ antique.origin }}</td>
              <td>{{ antique.century }}</td>
              <td>{{ antique.price }}</td>
              <td>{{ antique.category }}</td>
            </tr>
        </tbody>
      </table>
    </div>
  </section>

</template>

<script>

import axios from 'axios';

function compare(antique1,antique2){
  if(antique1.price > antique2.price){
    return 1;
  }
  if(antique1.price < antique2.price){
    return -1;
  }
  return 0;
}

  export default { 
    name: "antique",
    data() {
          return{
              antiques: {},
              sorts: {
                "1": "Low to High", 
                "-1": "High to Low"
              },
              sortSelected: null
          }
    },
    computed: {
      selected: {
        get() {
          return this.sortSelected;
        },
        set(v){
          this.sortSelected = v;
        }
      }
    },
    mounted() {
      this.getAntiques();
    },
    methods: {
      getAntiques(){
        axios.get(`http://localhost:3500/api/antiques/`).then(res => {
          this.antiques = res.data;
        });
      },
      searchName(){
        let name = document.getElementById("search-input").value;
        let filteredAntiques = [];
        for(let i = 0; i < this.antiques.length; i++){
          if(this.antiques[i].name.includes(name)){
            filteredAntiques.push(this.antiques[i]);
          }
        }
        this.antiques = filteredAntiques;      
      },
      clearFilters(){
        let input = document.getElementById("search-input").value = "";
        this.sortSelected = null;
        this.getAntiques();
      }, 
      orderPrice(sortType){
        if(sortType == 1){
          this.antiques.sort((a, b) => parseFloat(a.price) - parseFloat(b.price));
        }
        else if(sortType == -1){
          this.antiques.sort((a, b) => parseFloat(b.price) - parseFloat(a.price));
        }
      }
    }
}


</script>



<style scoped>

</style>
