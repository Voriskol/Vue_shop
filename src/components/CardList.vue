<script setup>
import TheCard from "./TheCard.vue";
defineProps({
  items: Array,
  isFavorites: Boolean,
});

const emit = defineEmits(["addToFavorite", "addToCart"]);
</script>

<template>
  <div v-auto-animate class="grid md:grid-cols-3 xl:grid-cols-4 lg:grid-cols-3 2xl:grid-cols-5 gap-5 grid-cols-1">
    <TheCard
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :price="item.price"
      :title="item.title"
      :image-url="item.imageUrl"
      :onClickFavorite="isFavorites ? null : () => emit('addToFavorite', item)"
      :on-click-add="isFavorites ? null : () => emit('addToCart', item)"
      :is-favorite="item.isFavorite"
      :is-added="item.isAdded"
    />
  </div>
</template>
