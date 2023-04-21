<template>
    <div id="app">
    <!-- <task v-bind:tasks="tasks"></task> -->
    <InputTask />
    <FooterComp class="footerComponente" />
  </div>
</template>

<script setup>
  import InputTask from './components/InputTask.vue';
  import FooterComp from "./components/FooterComp.vue"

</script>

<style>

</style>