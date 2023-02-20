<template>
  <div class="hello">
    <!-- <h1>{{ msg }}</h1>
    <p>
      Everything: {{ variable }}
        </p> -->


    <h1>DUCT POINTS</h1>

    Number of entries: <span v-if="data.features">{{ data.features.length }}</span>

    <ul v-if="data.features && data.features.length">
      <li style="display: block; margin-bottom: 20px;" v-for="feature in data.features" :key="feature.numid">
      
        <b>numid:</b> {{ feature.numid }},<br>
        <b>SYMBID:</b> {{ feature.properties.SYMBID }},<br>
      <b>USERCHANGED:</b> {{ feature.properties.USERCHANGED }} 

        <!-- <ul>
          <li style="display: block" v-for="(value, key) in feature.properties" :key="key">
            <span>{{ key }}: {{ value }}</span>
          </li>
        </ul> -->
      </li>
    </ul>
    <p v-else>Loading...</p>
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
      variable: 43,
      data: {},
      duct_points: [], //initialize with empty array
    }
  },
  mounted() {

    axios
      .get(
        "https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/nw_fixedaccess_demo/v1/planning/D_DUCT_POINT?id=10300000000008171"
      )
      .then((res) => {
        this.data = res.data;
      })
      .catch((err) => {
        console.log(err);
      });

  }

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
