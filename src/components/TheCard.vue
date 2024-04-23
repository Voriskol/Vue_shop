<script setup>
const props = defineProps({
  imageUrl: String,
  title: String,
  id: Number,
  price: Number,
  isFavorite: Boolean,
  isAdded: Boolean,
  onClickFavorite: Function,
  onClickAdd: Function,
});
</script>

<template>
  <div
    class="relative bg-white border border-slate-100 rounded-3xl p-8 cursor-pointer hover:-translate-y-2 hover:shadow-xl transition mt-10"
  >
    <img
      @click="props.onClickFavorite"
      :src="!isFavorite ? '/like-1.svg' : '/like-2.svg'"
      alt="like 1"
      class="absolute top-8 left-8"
    />
    <img :src="imageUrl" alt="Sneaker" />
    <p class="mt-2">{{ title }}</p>

    <div class="flex justify-between mt-5">
      <div class="flex flex-col">
        <span class="text-slate-400">Цена:</span>
        <b>{{ price }} руб.</b>
      </div>

      <div>
        <img
          @click="props.onClickAdd"
          :src="!isAdded ? '/plus.svg' : '/checked.svg'"
          alt="plus"
        />
      </div>
    </div>
  </div>
</template>
