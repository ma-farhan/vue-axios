<template>
  <v-container>
    <v-text-field v-model="apiUrl" label="Enter an API:" outlined dense></v-text-field>

    <v-btn @click="getAnswer">GET</v-btn>
    <v-btn @click="putAnswer">PUT</v-btn>
    <v-btn @click="postAnswer">POST</v-btn>
    <v-btn @click="patchAnswer">PATCH</v-btn>
    <v-btn @click="deleteAnswer">DELETE</v-btn>

    <div v-if="answer">
      <h3>Response:</h3>
      <pre>{{ answer }}</pre>
    </div>
  </v-container>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

const apiUrl = ref("");
const answer = ref(null);
const data1 = ref({
  "name": "Apple MacBook Pro 16",
  "data": {
    "year": 2019,
    "price": 1849.99,
    "CPU model": "Intel Core i9",
    "Hard disk size": "1 TB"
  }
})


const makeRequest = async (method) => {
  if (!apiUrl.value) {
    alert("Please enter a valid API URL");
    return;
  }

  try {
    const { data } = await axios({ url: apiUrl.value, method });
    answer.value = data;
  } catch (error) {
    console.error(error);
    answer.value = `Error: ${error.response?.data || error.message}`;
  }
};


const getAnswer = () => makeRequest("get");
const putAnswer = () => makeRequest("put");
const postAnswer = (data1) => makeRequest("post");
const patchAnswer = () => makeRequest("patch");
const deleteAnswer = () => makeRequest("delete");
</script>


