<script setup>
import TheCard from "./TheCard.vue";

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
      :price="5000"
      title="Мужские Кроссовки Nike Blazer Mid Suede"
      image-url="/sneakers/sneakers-1.jpg"
      :is-added="false"
      :is-favorite="false"
      :onClickAdd="onClickAdd"
      :onClickFavorite="onClickFavorite"
    />
  </div>
</template>
