<template>
  <div>
    <AskGPT />
  </div>
 </template>
 <script>
 import AskGPT from './components/AskGPT.vue'
  
 export default {
  name: 'App',
  components: {
    AskGPT
  }
 }
 </script>
 <style>
 #app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
 }
 </style>