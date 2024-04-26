<script setup>
defineProps({
  imageUrl: String,
  title: String,
  description: String,
});
</script>

<template>
  <div class="flex items-center flex-col max-w-80 mx-auto pt-20">
    <img class="h-50 w-50" :src="imageUrl" alt="Info image" />
    <h2 class="mt-4 text-2xl font-medium">{{ title }}</h2>
    <p class="text-slate-400 text-center">{{ description }}</p>
  </div>
</template>
