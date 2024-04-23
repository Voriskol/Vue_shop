<script setup>
import TheCard from "./TheCard.vue";
defineProps({
  items: Array,
});

const emit = defineEmits(["addToFavorite", "addToCart"]);
</script>

<template>
  <div class="grid grid-cols-4 gap-5">
    <TheCard
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :price="item.price"
      :title="item.title"
      :image-url="item.imageUrl"
      :onClickFavorite="() => emit('addToFavorite', item)"
      :on-click-add="() => emit('addToCart', item)"
      :is-favorite="item.isFavorite"
      :is-added="item.isAdded"
    />
  </div>
</template>
