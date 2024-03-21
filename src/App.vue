
<template>

  <section id="title_section">
  </section>

  <section id="hero_section">

  </section>

  <section id="navbar_section">

  </section>

  <section id="sort_perpage_section">

  </section>

  <section id="display_section">

  </section>


  <section id="footer_section">

  </section>

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

      <h2>Filter By Category:</h2>

      <div id="filterbox">
        <div id="category-filter" v-for="(val,key) in categories">
          <input type="checkbox" :name="val" :value="key" v-model="categoriesSelected" @change="filterByCategories(categoriesSelected)">
          <label :for="val">{{ val }}</label>
        </div>
      </div>

      <h2>Items Per Page:</h2>
      <div id="page-box">
        <div id="items-per-page-box" v-for="val in itemsPerPage">
          <button :name="val" @click="updateNumItems(val)">{{ val }}</button>
        </div>
        <h2 class="sky-400">Page:</h2>
        <button id="to-first" @click="displayPageAntiques(1)"> &lt;&lt; </button>
        <button id="to-prev" @click="displayPageAntiques(curPage-1)"> &lt; </button>
        <div id="pagination" v-for="i in numPages">
          <button @click="displayPageAntiques(i)">{{ i }}</button>
        </div>
        <button id="to-next" @click="displayPageAntiques(curPage+1)"> &gt; </button>
        <button id="to-last" @click="displayPageAntiques(numPages)"> &gt;&gt; </button>
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
            <tr v-for="(antique, index) in curAntiques" :key="index">
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
                1: "Porcelain and Ceramics ",
                2: "Framed Art",
                3: "Statues and Figurines",
                4: "Clocks",
                5: "Furniture and Decor",
                6: "Carpets",
                7: "Other"
              },
              sortSelected: null,
              categoriesSelected: [],
              titleOrDesc: false, 
              curAntiques: [],
              itemsPerPage: [20,50,100],
              itemsPerPageSelected: 20,
              numPages: 8,
              curPage: 1
          }
    },
    mounted() {
      this.getAntiques();
    },
    methods: {
      getAntiques(){
        axios.get(`http://localhost:3500/api/antiques/`).then(res => {
          this.antiques = res.data;
          this.updateNumPages();  
          this.displayPageAntiques(1);
        });
      },
      displayPageAntiques(pageNum){
        if((pageNum > 0) && (pageNum <= this.numPages)){
          this.curPage = pageNum;
          this.curAntiques = [];
          let allAntiques = this.proxyToJSON(this.antiques);
          let idx = (this.curPage-1) * this.itemsPerPageSelected;
          let endIdx  = (this.curPage < this.numPages) ? this.curPage * this.itemsPerPageSelected : allAntiques.length;
          for(idx; idx < endIdx; idx++){
            this.curAntiques.push(allAntiques[idx]);
          }
        }
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
        this.updateNumPages();   
        this.displayPageAntiques(1);
      },
      clearFilters(){
        document.getElementById("search-input").value = "";
        this.sortSelected = null;
        this.categoriesSelected = [];
        this.titleOrDesc = false;
        this.getAntiques();
      }, 
      orderPrice(sortType){
        if(sortType == 1){
          this.antiques.sort((a, b) => parseFloat(a.price) - parseFloat(b.price));
        }
        else if(sortType == -1){
          this.antiques.sort((a, b) => parseFloat(b.price) - parseFloat(a.price));
        }
        this.displayPageAntiques(this.curPage);
      },
      filterByCategories(){
        axios.get(`http://localhost:3500/api/antiques/`).then(res => {
          this.antiques = res.data;

          let categoryTypes = this.proxyToJSON(this.categoriesSelected);

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
            this.orderPrice(this.sortSelected);
            this.updateNumPages();
            this.displayPageAntiques(1); 
          }
        });
      },
      proxyToJSON(proxyArr){
        return JSON.parse(JSON.stringify(proxyArr));
      },
      updateNumItems(newItems){
        this.itemsPerPageSelected = newItems;
        this.displayPageAntiques(1);
        this.updateNumPages();
      },
      updateNumPages(){
        this.numPages = Math.ceil(this.antiques.length/this.itemsPerPageSelected);
      }
    }
}

</script>

<style scoped>

@font-face {
  font-family: title;
  src: url("fonts/Atlas\ Regular.ttf");
}

</style>
