<template>
  <div class="hello">

    <h1>{{ msg }}</h1>
    <h2>List the ducts</h2>
    <!-- {{ apexData.items }} <br><br> -->

    <!-- <select @change="getDuctPointData" v-model="selectedDuctId" > -->
    <select @change="getDuctPoints" v-model="selectedDuctId">
      <option value="">Select Duct</option>
      <!-- <option v-for="planId in planIds" :key="planId.empno">{{  planId.NUMID }}</option> -->

      <option v-for="ductItem in ducts" :key="ductItem.duct_id">{{ ductItem.duct_id }}</option>

    </select>

    <div v-if="apexData.items && apexData.items.length" style="text-align: center; margin-top: 20px;">


      <form method="post" style="width:300px; margin: 0 auto; text-align: left" @submit.prevent="submitForm">
        <!-- <b>Duct ID</b>  {{ apexData.items[0].duct_id }}<br><br> -->
        <label for="company_name">Company name </label>
        <input id="company_name" type="text" name="company_name" v-model="companyName" /><br><br>

        <label for="duct_lat">Latitude </label>
        <input id="duct_lat" type="text" name="duct_lat"  v-model="duct_lat" /><br><br>
        <label for="duct_long">Longitude </label>
        <input id="duct_long" type="text" name="duct_long"  v-model="duct_long"/><br>
        <button style="align-items: center; margin-top:10px" type="submit">Update Duct</button>
      </form>
      
    </div>
    <p v-else>Please choose from above</p>
    <!-- <h2>Or add new duct</h2>

<button>Add new duct</button>      
<input /> -->

  </div>
</template>
<script>
import axios from 'axios'; // import the axios library
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      apexData: {},
      selectedDuctId: '',
      planIds: [],
      ducts: [],
      
      companyName: '',
    ductLat: '',
    ductLong: '',
    ductId: 0
    }
  },

  methods: {
    getDuctPoints() {
      if (this.selectedDuctId) {
        axios.get(`
        https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/one?DUCT_ID=${this.selectedDuctId}`)
          .then(response => {
            this.apexData = response.data;
          })
          .catch(error => {
            console.log(error);
          });
      } else {
        this.apexData = ""
      }
    },




    submitForm() {
      const data = {
        COMPANY_NAME: this.companyName,
        DUCT_LAT: this.ductLat,
        DUCT_LONG: this.ductLong,
        DUCT_ID: this.ductId,
      };
      // make a POST request to the server with the form data
      axios.post('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/update', data)
        .then(response => {
          // handle the response as needed
          console.log(response);
        })
        .catch(error => {
          // handle any errors
          console.log(error);
        });
    },

  },


  mounted() {
    axios.get('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/info')
      .then(response => {
        this.ducts = response.data.items; // assuming the response data is an object with an "items" property that contains the array of employees
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
