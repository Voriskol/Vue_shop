<script setup>
import { onMounted, ref, watch, reactive, provide, computed } from "vue";
import axios from "axios";
import TheHeader from "./components/TheHeader.vue";
import CardList from "./components/CardList.vue";
import TheDrawer from "./components/TheDrawer.vue";

const items = ref([]); // state хранящий все наши кроссовки. ref для хранения массива
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

// реактивный state хранящий наши фильтры. reactive для хранения объекта
const filters = reactive({
  sortBy: "title",
  searchQuery: "",
});

const addToCart = (item) => {
  cart.value.push(item);
  item.isAdded = true;
};

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item));
  item.isAdded = false;
};

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
  console.log(cart);
};

// функции следящие за изменением селекта и наших фильтров
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
};

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value;
};

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(
      `https://666e03bcd9ab9fc4.mokky.dev/favorites`
    );
    items.value = items.value.map((item) => {
      const favorite = favorites.find(
        (favorite) => favorite.parentId === item.id
      );

      if (!favorite) {
        return item;
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      };
    });
  } catch (err) {
    console.log(err);
  }
};

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id,
      };
      item.isFavorite = true;
      const { data } = await axios.post(
        `https://666e03bcd9ab9fc4.mokky.dev/favorites`,
        obj
      );

      item.favoriteId = data.id;
    } else {
      axios.delete(
        `https://666e03bcd9ab9fc4.mokky.dev/favorites/${item.favoriteId}`
      );
      item.isFavorite = false;
      item.favoriteId = null;
    }
  } catch (err) {
    console.log(err);
  }
};

//функция которая при каждом изменении фильтров или при первом рендере выполняет запрос на бэкенд
const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
    };
    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`;
    }
    const { data } = await axios.get(
      `https://666e03bcd9ab9fc4.mokky.dev/items`,
      {
        params,
      }
    );

    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false,
    }));
  } catch (err) {
    console.log(err);
  }
};

onMounted(async () => {
  await fetchItems();
  await fetchFavorites();
});

watch(filters, fetchItems);

provide("cart", { cart, closeDrawer, openDrawer, addToCart, removeFromCart });
</script>

<template>
  <TheDrawer
    v-if="drawerOpened"
    :total-price="totalPrice"
    :vat-price="vatPrice"
  />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <TheHeader :total-price="totalPrice" @open-drawer="openDrawer" />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
        <div class="flex items-center gap-5">
          <select
            @change="onChangeSelect"
            name=""
            id=""
            class="border rounded-xl py-2 h-10 outline-none focus:border-slate-300 cursor-pointer text-slate-400"
          >
            <option>Сортировка</option>
            <option value="name">По названию</option>
            <option value="price">По цене (сначала дешевле)</option>
            <option value="-price">По цене (сначала дороже)</option>
          </select>

          <div class="relative">
            <img class="absolute top-3 left-4" src="/search.svg" alt="" />
            <input
              @input="onChangeSearchInput"
              type="text"
              placeholder="Поиск..."
              class="border rounded-xl py-2 h-10 pl-10 pr-4 outline-none focus:border-slate-300"
            />
          </div>
        </div>
      </div>

      <CardList
        :items="items"
        @add-to-favorite="addToFavorite"
        @add-to-cart="onClickAddPlus"
      />
    </div>
  </div>
</template>
