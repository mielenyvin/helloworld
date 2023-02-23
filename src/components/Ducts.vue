<template>
  <div class="hello">

    <h1>{{ msg }}</h1>
    <span :class="{ 'text-green': onlineStatus === 'Online work', 'text-red': onlineStatus === 'Offline work' }">{{ onlineStatus }}</span>
    <button @click="switchOnOffline()" style="margin-left:10px" >  {{ buttonOnlineText }} </button>
    
    <h2>Get or update the ducts</h2>
    <!-- {{ apexData.items }} <br><br> -->

    <!-- <select @change="getDuctPointData" v-model="selectedDuctId" > -->
    <select @change="getDuctPoints" v-model="selectedDuctId">
      <option value="">Select Duct</option>
      <!-- <option v-for="planId in planIds" :key="planId.empno">{{  planId.NUMID }}</option> -->

      <option v-for="ductItem in ducts" :key="ductItem.duct_id">{{ ductItem.duct_id }}</option>

    </select>

    <div v-if="apexData.items && apexData.items.length" style="text-align: center; margin-top: 20px;">


      <form method="post" style="width:300px; margin: 0 auto; text-align: left" @submit.prevent="updateDucts">
        <label for="company_name">Company name </label>
        <input id="company_name" type="text" name="company_name" v-model="companyName" /><br><br>
        <label for="duct_lat">Latitude </label>
        <input id="duct_lat" type="text" name="duct_lat" v-model="ductLat" /><br><br>
        <label for="duct_long">Longitude </label>
        <input id="duct_long" type="text" name="duct_long" v-model="ductLong" /><br>
        <button style="align-items: center; margin-top:10px" type="submit">Update Duct</button>
      </form>

    </div>
    <p v-else>Please choose from above</p>
    
    <h2>Or add new duct</h2>

    <div v-if="ducts" style="text-align: center; margin-top: 20px;">

<form method="post" style="width:300px; margin: 0 auto; text-align: left" @submit.prevent="addDucts">

  <label for="duct_id">Duct id </label>
  <input  disabled  id="duct_id" type="text" name="duct_id" v-model="ductIdNew"  /><br><br>
  
  <label for="company_name">Company name </label>
  <input  id="company_name" type="text" name="company_name" v-model="companyNameNew" /><br><br>
  <label for="duct_lat">Latitude </label>
  <input id="duct_lat" type="text" name="duct_lat" v-model="ductLatNew" /><br><br>
  <label for="duct_long">Longitude </label>
  <input id="duct_long" type="text" name="duct_long" v-model="ductLongNew" /><br>
  <button style="align-items: center; margin-top:10px" type="submit">Add Duct</button>
</form>

</div>


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
      ductId: 0,
      companyNameNew: '',
      ductLatNew: '',
      ductLongNew: '',
      ductIdNew: 0,
      onlineStatus: "Offline work",
    }
  },

  computed: {
    buttonOnlineText() {
      return this.onlineStatus === "Online work" ? "Go Offline" : "Go Online";
    }
  },

  methods: {
    getDuctPoints() {
      if (this.selectedDuctId) {
        axios.get(`
        https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/one?DUCT_ID=${this.selectedDuctId}`)
          .then(response => {
            this.apexData = response.data;
            this.companyName = this.apexData.items[0].company_name;
            this.ductLat = this.apexData.items[0].duct_lat;
            this.ductLong = this.apexData.items[0].duct_long;
            this.ductId = this.selectedDuctId;
          })
          .catch(error => {
            console.log(error);
          });

      } else {
        this.apexData = ""
      }
    },

    addDucts() {
      const data2 = {
        COMPANY_NAME: this.companyNameNew,
        DUCT_LAT: this.ductLatNew,
        DUCT_LONG: this.ductLongNew,
        DUCT_ID: this.ductIdNew,
      };
      // make a POST request to the server with the form data
      axios.post('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/info', data2)
        .then(response => {
          // handle the response as needed
          console.log(response);
          this.fetchData();
        })
        .catch(error => {
          // handle any errors
          console.log(error);
        });
    },

    updateDucts() {
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

    switchOnOffline(){
      if (this.onlineStatus == "Offline work") {
        this.onlineStatus = "Online work"
      }
      else
      this.onlineStatus = "Offline work"
      
    },

    fetchData() {
      axios.get('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/info')
      .then(response => {
        this.ducts = response.data.items; // assuming the response data is an object with an "items" property that contains the array of employees
        this.ductIdNew = (this.ducts[this.ducts.length-1].duct_id +1)
      })
      .catch(error => {
        console.error(error);
      });
      this.companyNameNew = '',
      this.ductLatNew = '',
      this.ductLongNew = '',
      this.ductIdNew = (this.ducts[this.ducts.length-1].duct_id +1)
    }

  },


  mounted() {
    const confirmLoad = confirm("Do you want to try to load information from the server?");
    
  
    if (confirmLoad) {
      this.onlineStatus = "Online work"
      axios.get('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/info')
        .then(response => {
          this.ducts = response.data.items; // assuming the response data is an object with an "items" property that contains the array of employees
          this.ductIdNew = (this.ducts[this.ducts.length-1].duct_id +1)
        })
        .catch(error => {
          console.error(error);
          this.onlineStatus = "Offline work"
        });
    } else {
      this.onlineStatus = "Offline work"
      // load data from internal source
    }
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
  .text-green {
    color: green;
  }
  
  .text-red {
    color: red;
  }
  </style>
  
