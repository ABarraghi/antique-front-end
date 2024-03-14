
<template>
  <section>
    <div>
      <input type="text" placeholder="Search for an Antique" id="search-input">
      <input type="checkbox" id="or-check" v-model="titleOrDesc">
      <label for="or-check">Search Both Title and Description</label>
      <button id="search-btn" @click="search()">Search</button>
      <button id="clear-btn" @click="clearFilters()">Clear</button>

      <div id="sort-box" v-for="(val, key) in sorts">
        <input type="radio" name="radio" :value="key" v-model="sortSelected" :id="key" @change="orderPrice(sortSelected)">
        <label :for="val">{{ val }}</label>
      </div>

      <h2>Filters:</h2>

      <div id="filterbox">
        <h4>By Category:</h4>
        <div id="category-filter" v-for="(val,key) in categories">
          <input type="checkbox" :name="val" :value="key" v-model="categoriesSelected" @change="filterByCategories(categoriesSelected)">
          <label :for="val">{{ val }}</label>
        </div>
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

  export default { 
    name: "antique",
    data() {
          return{
              antiques: {},
              sorts: {
                "1": "Low to High", 
                "-1": "High to Low"
              },
              categories: {
                1: "Porcelain and Cermaics",
                2: "Framed Art",
                3: "Statues and Figurines",
                4: "Clocks",
                5: "Furniture and Decor",
                6: "Carpets",
                7: "Other"
              },
              sortSelected: null,
              categoriesSelected: [],
              titleOrDesc: false
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
      search(){
        
        let token = document.getElementById("search-input").value;
        let filteredAntiques = [];
        for(let i = 0; i < this.antiques.length; i++){
          if(this.titleOrDesc){
            if(this.antiques[i].name.includes(token) || this.antiques[i].description.includes(token)){
            filteredAntiques.push(this.antiques[i]);
            }
          }
          else {
            if(this.antiques[i].name.includes(token)){
            filteredAntiques.push(this.antiques[i]);
            }
          }
        }
        this.antiques = filteredAntiques;      
      },
      clearFilters(){
        let input = document.getElementById("search-input").value = "";
        this.sortSelected = null;
        this.categoriesSelected = [];
        this.getAntiques();
      }, 
      orderPrice(sortType){
        if(sortType == 1){
          this.antiques.sort((a, b) => parseFloat(a.price) - parseFloat(b.price));
        }
        else if(sortType == -1){
          this.antiques.sort((a, b) => parseFloat(b.price) - parseFloat(a.price));
        }
      },
      filterByCategories(){
        axios.get(`http://localhost:3500/api/antiques/`).then(res => {
          this.antiques = res.data;

          let categoryTypes = JSON.parse(JSON.stringify(this.categoriesSelected));

          for(let i = 0; i < categoryTypes.length; i++ ){
            categoryTypes[i] = Number(categoryTypes[i]);
          }

          let filteredAntiques = [];
          for(let i = 0; i < this.antiques.length; i++){
            if(categoryTypes.includes(this.antiques[i].category)){
              filteredAntiques.push(this.antiques[i]);
            }
          }

          if(filteredAntiques.length!=0){
            this.antiques = filteredAntiques; 
          }
          
          this.orderPrice(this.sortSelected);
        });
      },
    }
}


</script>



<style scoped>

</style>
