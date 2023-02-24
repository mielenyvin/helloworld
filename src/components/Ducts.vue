<template>
  <!-- #TODO - to create offline DUCTS with id 555prefix. then when go online check if they are exist -->
  <!-- #TODO - check if data was really updated -->

  <div class="hello">

    <h1>{{ msg }} 1.3</h1>
    <span :class="{ 'text-green': onlineStatus === 'Online work', 'text-red': onlineStatus === 'Offline work' }">{{
      onlineStatus }}</span>
    <button @click="switchOnOffline()" style="margin-left:10px"> {{ buttonOnlineText }} </button>

    <h2>Get or update the ducts</h2>
    <span><b>All ducts:</b></span>
    {{ ducts }} <br><br>
    <span><b>IDs of ducts updated in offline mode:</b></span>{{ editedOfflineDuctIds }} <br><br>

    <!-- <select @change="getDuctPointData" v-model="selectedDuctId" > -->
    <select @change="getDuctPoints" v-model="selectedDuctId">
      <option value="">Select Duct</option>
      <option v-for="ductItem in ducts" :key="ductItem.duct_id">{{ ductItem.duct_id }}</option>
    </select>

    <div v-if="ducts.length || apexData.items" style="text-align: center; margin-top: 20px;">


      <form method="post" style="width:300px; margin: 0 auto; text-align: left" @submit.prevent="updateDuct">
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
        <input disabled id="duct_id" type="text" name="duct_id" v-model="ductIdNew" /><br><br>

        <label for="company_name">Company name </label>
        <input id="company_name" type="text" name="company_name" v-model="companyNameNew" /><br><br>
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
      editedOfflineDuctIds: []
    }
  },

  computed: {
    buttonOnlineText() {
      return this.onlineStatus === "Online work" ? "Go Offline" : "Synchronize and go Online";
    }
  },

  methods: {
    getDuctPoints() {
      if (this.onlineStatus === "Offline work") {

        if (this.selectedDuctId) {
          this.ductId = this.selectedDuctId;
          this.companyName = this.ducts[this.selectedDuctId - 1].company_name;
          this.ductLat = this.ducts[this.selectedDuctId - 1].duct_lat;
          this.ductLong = this.ducts[this.selectedDuctId - 1].duct_long;
        }


      }
      else {
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
              this.onlineStatus = "Offline work"
            });

        } else {
          this.apexData = ""
        }

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

    updateDuct() {
      if (this.onlineStatus === "Offline work") {
        // if offline, add ID of updated duct
        if (this.editedOfflineDuctIds.indexOf(parseInt(this.selectedDuctId)) === -1) {
          this.editedOfflineDuctIds.push(parseInt(this.selectedDuctId));
        }
      }
      else {
        // if online, send request to database with new data
        this.sendDataToServer(parseInt(this.ductId), this.companyName, parseFloat(this.ductLat), parseFloat(this.ductLong))
      }
      // Here we need to update the duct[] data whether for offline or online.

      // eslint-disable-next-line
      var updatedItem = {
        "duct_id": this.ductId,
        "company_name": this.companyName,
        "duct_lat": this.ductLat,
        "duct_long": this.ductLong
      };

      // eslint-disable-next-line

      const index = this.ducts.findIndex((item) => item.duct_id == updatedItem.duct_id);
      if (index !== -1) {
        // Use $set to update the company_name of the element at the index
        this.$set(this.ducts[index], "company_name", updatedItem.company_name);
        this.$set(this.ducts[index], "duct_lat", parseFloat(updatedItem.duct_lat));
        this.$set(this.ducts[index], "duct_long", parseFloat(updatedItem.duct_long));
      }

    },

    sendDataToServer(ductId, companyName, ductLat, ductLong) {

      // Online request to database
      const data = {
        DUCT_ID: parseInt(ductId),
        COMPANY_NAME: companyName,
        DUCT_LAT: parseFloat(ductLat),
        DUCT_LONG: parseFloat(ductLong)
      };


      // make a POST request to the server with the form data
      axios.post('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/update', data)
        .then(response => {
          this.fetchData();
          console.log(response);
          this.editedOfflineDuctIds.splice(0);
          this.onlineStatus = "Online work";
        })
        .catch(error => {
          console.log(error);
          this.onlineStatus = "Offline work"
        });

    },

    switchOnOffline() {
      if (this.onlineStatus == "Offline work") {
        // if we were offline, we need to 
        // 1) send updated data on the server (if there is) 

        // if there are some data, which was update in offline mode
        if (this.editedOfflineDuctIds.length > 0) {

        const editedDuctIds = this.editedOfflineDuctIds.map(id => parseInt(id));

        // Filter the ducts array to only include items with duct IDs that are in the editedDuctIds array
        const ductsToUpdate = this.ducts.filter(item => editedDuctIds.includes(item.duct_id));

        Promise.all(ductsToUpdate.map(item => {
          return this.sendDataToServer(parseInt(item.duct_id), item.company_name, parseFloat(item.duct_lat), parseFloat(item.duct_long));
        }))
          .then(() => {
            // do something else if needed
          })
          .catch(error => {
            console.error(error);
            // handle the error if needed
          });
        } 
        // if there is no  data, which was update in offline mode
        else
{
  this.fetchData()
}
      }
      else
        // if we were online, just switch the variable
        this.onlineStatus = "Offline work"
    },

    fetchData() {
      axios.get('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/info')
        .then(response => {
          this.ducts = response.data.items; // assuming the response data is an object with an "items" property that contains the array of employees
          this.ductIdNew = (this.ducts[this.ducts.length - 1].duct_id + 1)
          this.companyNameNew = '',
            this.ductLatNew = '',
            this.ductLongNew = '',
            this.ductIdNew = (this.ducts[this.ducts.length - 1].duct_id + 1)
          this.onlineStatus = "Online work"
        })
        .catch(error => {
          console.error(error);
          // #TODO: tut nado poimat oshibku not internet
          // this.onlineStatus = "Offline work"
        });

    }

  },


  mounted() {
    // const confirmLoad = confirm("Do you want to try to load information from the server?");
    // if (confirmLoad) {
    // eslint-disable-next-line
    if (this.ducts.length < 0) {

      axios.get('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/ducts/info')
        .then(response => {
          this.ducts = response.data.items; // assuming the response data is an object with an "items" property that contains the array of employees
          this.ductIdNew = (this.ducts[this.ducts.length - 1].duct_id + 1)
          this.onlineStatus = "Online work"
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
  
