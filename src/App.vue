<script setup>
import TheHeader from "./components/TheHeader.vue";
import CardList from "./components/CardList.vue";
import TheDrawer from "./components/TheDrawer.vue"
</script>

<template>
  <TheDrawer />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <TheHeader />

    <div class="p-10">
      <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

      <CardList />
    </div>
  </div>
</template>
