<script setup>
import { ref, watch, provide, computed } from "vue";
import axios from "axios";
import TheHeader from "./components/TheHeader.vue";
import TheDrawer from "./components/TheDrawer.vue";

/* Корзина */
const cart = ref([]);

const drawerOpened = ref(false);

const totalPrice = computed(() =>
  cart.value.reduce((acc, item) => acc + item.price, 0)
);

const vatPrice = computed(() => Math.round(totalPrice.value * 0.05));

const closeDrawer = () => {
  drawerOpened.value = false;
};

const openDrawer = () => {
  drawerOpened.value = true;
};

const addToCart = (item) => {
  cart.value.push(item);
  item.isAdded = true;
};

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1);
  item.isAdded = false;
};

const createOrder = async () => {
  try {
    const { data } = await axios.post(
      "https://666e03bcd9ab9fc4.mokky.dev/orders",
      {
        items: cart.value,
        totalPrice: totalPrice.value,
      }
    );
    let order_id = data.id;
    cart.value = [];
    alert(`Заказ успешно оформлен. Номер вашего заказа: ${order_id}`);
    return { ...data, order_id };
  } catch (err) {
    console.log(err);
  }
};

watch(
  cart,
  () => {
    localStorage.setItem("cart", JSON.stringify(cart.value));
  },
  { deep: true }
);

provide("cart", { cart, closeDrawer, openDrawer, addToCart, removeFromCart });
/* Корзина (END)*/
</script>

<template>
  <TheDrawer
    v-if="drawerOpened"
    :total-price="totalPrice"
    :vat-price="vatPrice"
    @create-order="createOrder"
  />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <TheHeader :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <RouterView />
    </div>
  </div>
</template>
