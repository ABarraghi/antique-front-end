<template>
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

</template>

<script>

import axios from 'axios';

  export default { 
    name: "antique",
    data() {
          return{
              antiques: {},
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
      }
    }
}


</script>



<style scoped>

</style>
