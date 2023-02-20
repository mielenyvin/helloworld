<template>
    <div class="hello">

        <h1>EMPLOYEES</h1>
  
      <ul>
        <li style="display: block; margin-bottom:10px" v-for="employee in employees" :key="employee.empno">Name: {{ employee.ename }} Position: {{ employee.job }}</li>
      </ul>
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
        employees: []
      }
    },
    mounted() {
      axios.get('https://g0268f6dc90ba0e-devdb.adb.eu-frankfurt-1.oraclecloudapps.com/ords/apex_dmitrii/hr/employees/')
        .then(response => {
          this.employees = response.data.items; // assuming the response data is an object with an "items" property that contains the array of employees
        })
        .catch(error => {
          console.error(error);
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
  }</style>
  