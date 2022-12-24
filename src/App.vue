<script setup>
import { ref, watch } from 'vue'

const searchTerm = ref('')
let startTime = 0;
let functionCalledInWindow = false;

const debounce = async (func, delay) => { 
  if(!functionCalledInWindow) 
  {
    startTime = Date.now();
    functionCalledInWindow = true;
    await func();
  }
  else{
    if(Date.now() - startTime > delay)  
    {
      console.log(Date.now() - startTime)
      functionCalledInWindow = false;
      await func();
    }
  }
 }

const findProducts = async term =>  debounce(() => { setTimeout( () => console.log('newTerm ', term), 1000)}, 1000)

watch(searchTerm, newTerm => findProducts(newTerm))
</script>

<template>
  <div class="w-full h-full flex flex-col gap-5 justify-center items-center">
    <h1 class="text-4xl font-bold">Gift Search Bar</h1>
    <input type="text" class="p-2 border-2 border-gray-dark" v-model="searchTerm" placeholder="Start typing..." />
    <ul class="list-disc">
      <li>Display suggestions here</li>
    </ul>
  </div>
</template>
