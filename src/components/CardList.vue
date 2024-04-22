<script setup>
import TheCard from "./TheCard.vue";
defineProps({
  items: Array,
});

const onClickAdd = () => {
  alert("Добавить");
};

const onClickFavorite = () => {
  alert("В закладки");
};
</script>

<template>
  <div class="grid grid-cols-4 gap-5">
    <TheCard
      v-for="item in items"
      :key="item.id"
      :price="item.price"
      :title="item.title"
      :image-url="item.imageUrl"
      :is-added="item.isAdded"
      :is-favorite="false"
      :onClickAdd="onClickAdd"
      :onClickFavorite="onClickFavorite"
    />
  </div>
</template>
