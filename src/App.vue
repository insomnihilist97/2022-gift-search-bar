<script setup>
//spinner css shameless stolen from https://www.w3schools.com/howto/howto_css_loader.asp

import { ref, watch } from 'vue'

const searchTerm = ref('');
const productList = ref([]);
let resultsFound = ref(true);
let resultsLoading = ref(false);
let startTime = 0;
let funcCalled = false;
let funcId = null;
let resetId = null;

const debounce = async (func, delay) => { 
  if(!funcCalled)
  {
    funcCalled = true;
    startTime = Date.now();
    funcId = setTimeout(func, delay);
    resetId = setTimeout(() => funcCalled = false, delay);
  }
  else if(Date.now() - startTime < delay) {
    clearTimeout(funcId);
    clearTimeout(resetId);
    startTime = Date.now();
    funcId = setTimeout(func, delay);
    resetId = setTimeout(() => funcCalled = false, delay);
  }
}

const callDummyAPI = async searchParam => {
  if(searchParam.trim().length === 0)
    {
      productList.value = [];
      resultsFound.value = true;
      return;
    }
  try{
    resultsLoading.value = true;
    await fetch('https://dummyjson.com/products/search?q=' + searchParam).
    then((results) => results.json().then((result) => {
      resultsLoading.value = false;
      if(result.limit > 0)
      {
        productList.value = result.products;
        resultsFound.value = true;
      }
      else resultsFound.value = false;
    }))
  }
  catch(error) {
    alert('Search failed with error ' + error);
  }
}

const findProducts = async term => debounce(() => callDummyAPI(term), 300)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <ul v-if="resultsFound" class="list-disc">
      <li v-for="product in productList">
        {{ product.title }} - ${{ product.price }}
      </li>
    </ul>
    <p v-if="!resultsFound" style="color: red">No results found</p>
    <div v-if="resultsLoading" class="spinner"></div>
  </div>
</template>

<style>
.spinner {
  border: 16px solid #f3f3f3; /* Light grey */
  border-top: 16px solid #3498db; /* Blue */
  border-radius: 50%;
  width: 2.5rem;
  height: 2.5rem;
  animation: spin 2s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}
</style>
