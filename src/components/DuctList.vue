<template>
  <div class="hello">

    <h1> {{ msg }}</h1>

    <select @change="getDuctPointData" v-model="selectedPlanning" >
        <option value="">Select Planning</option>
        <option v-for="planId in planIds" :key="planId.empno">{{  planId.NUMID }}</option>
      </select>

   
      <ul v-if="apexData.features && apexData.features.length">

        Number of entries: <span v-if="apexData.features">{{ apexData.features.length }}</span><br><br>

        <li style="display: block; margin-bottom: 20px;" v-for="feature in apexData.features" :key="feature.numid">
          <b>numid:</b> {{ feature.numid }},<br>
          <b>SYMBID:</b> {{ feature.properties.SYMBID }},<br>
          <b>USERCHANGED:</b> {{ feature.properties.USERCHANGED }} <br>
          <b>lat</b> {{ feature.geometry.coordinates[0] }}<br>
          <b>long</b> {{feature.geometry.coordinates[1]  }}
        </li>
      </ul>
      <p v-else>Please choose from above</p>

      
  </div>
</template>
<script>
import axios from 'axios'; // import the axios library
export default {
  name: 'DuctList',
  props: {
    msg: String
  },
  data() {
    return {
      apexData: {},
      selectedPlanning: '',
      planIds: [],
    }
  },

  methods: {
      getDuctPointData() {
        if (this.selectedPlanning) {
        axios.get(`https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/nw_fixedaccess_demo/v1/planning/D_DUCT_POINT?id=${this.selectedPlanning}`)
          .then(response => {
            this.apexData = response.data;
          })
          .catch(error => {
            console.log(error);
          });
      } else {
        this.apexData = ""
      }
    }
    },
  

  mounted() {
    axios.get('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/nw_fixedaccess_demo/v1/planning/GETALLPLANNINGS')
      .then(response => {
        this.planIds = response.data; // assuming the response data is an object with an "items" property that contains the array of employees
      })
      .catch(error => {
        console.error(error);
      });
  },


}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}
</style>
