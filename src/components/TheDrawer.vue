<script setup>
import DrawerHead from "./DrawerHead.vue";
import CartItemList from "./CartItemList.vue";
import InfoBlock from "./infoBlock.vue";

const emit = defineEmits(["createOrder"]);
defineProps({
  totalPrice: Number,
  vatPrice: Number,
});
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />

    <InfoBlock
      v-if="!totalPrice"
      title="Корзина пустая"
      description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ."
      image-url="/package-icon.png"
    />

    <CartItemList />

    <div v-if="totalPrice" class="flex flex-col gap-4 mb-6 my-7">
      <div class="flex justify-between gap-2">
        <span>Итого: </span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>{{ totalPrice }} руб.</b>
      </div>

      <div class="flex justify-between gap-2">
        <span>Налог 5 %</span>
        <div class="flex-1 border-b border-dashed"></div>
        <b>{{ vatPrice }} руб.</b>
      </div>
    </div>
    <div v-if="totalPrice">
      <button
        @click="() => emit('createOrder')"
        class="bg-lime-500 w-full rounded-xl py-3 text-white cursor-pointer hover:shadow-lg transition hover:-translate-y-2 hover:bg-lime-600 active:bg-lime-700"
      >
        Оформить заказ
      </button>
    </div>
  </div>
</template>
