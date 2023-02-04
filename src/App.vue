<template>
  {{ apiLog }} <br>
  <items ref="myProducts" :items="products" v-slot="{ item }">
    <input placeholder="id" size="1" type="text" v-model="item.data.id">
    <input placeholder="title" size="5" type="text" v-model="item.data.title">
    <button @click="save(item)">save</button>
    <button @click="savePut(item)">save(PUT)</button>
    <button @click="del(item)">delete</button>
  </items>
  <button @click="$refs.myProducts.addNew({})">+Add New</button>
</template>

<script>

import items from '@benixal/vue-items'
import axios from 'axios'
export default {
  name: 'App',
  components: {
    items
  }, mounted() {
    axios.get("http://127.0.0.1:8060/products")
      .then(response => (this.products = response.data))
  }, methods: {
    save(item) {
      if (!Object.keys(item.data).includes("id")) {
        axios.post("http://127.0.0.1:8060/products", item.data)
          .then((response) => {
            item.data.id = response.data.id;
            this.apiLog = response.data;
          })
      } else {

        axios.patch("http://127.0.0.1:8060/products/" + item.data.id,
         item.data
         )
          .then((response) => {
            this.apiLog = response.data;
          })
      }
    },
    savePut(item) {
      axios.put("http://127.0.0.1:8060/products/" + item.data.id,
       {id:item.data.id,name:item.data.title}
       )
        .then((response) => {
          this.apiLog = response.data;
        })
    }
    , del(item) {
      axios.delete("http://127.0.0.1:8060/products/" + item.data.id)
        .then((response) => {
          item.destroy();
          this.apiLog = response.data;
        })
    }
  },
  data() {
    return {
      apiLog: null,
      products: []
    }
  },
}
</script>

<style>
button , input {margin:0.1rem}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
