<script setup>
import { ref, watch } from 'vue'

const searchTerm = ref('');
const productList = ref([]);
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
  const results = await fetch('https://dummyjson.com/products/search?q=' + searchParam);
  console.log(results.json());
  productList.value = results.products;
}

const findProducts = async term => debounce(() => callDummyAPI(term), 300)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <ul class="list-disc">
      <li v-for="product in productList">
        {{ product.id }}
      </li>
    </ul>
  </div>
</template>
