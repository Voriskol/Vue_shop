<script setup>
import { inject, reactive, watch, ref, onMounted } from "vue";
import CardList from "../components/CardList.vue";
import axios from "axios";
import debounce from "lodash.debounce";

const { cart, addToCart, removeFromCart } = inject("cart");

const items = ref([]); // state хранящий все наши кроссовки. ref для хранения массива

// реактивный state хранящий наши фильтры. reactive для хранения объекта
const filters = reactive({
  sortBy: "title",
  searchQuery: "",
});

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
};

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value;
}, 1000);
const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        item_id: item.id,
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

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item);
  }
};
const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(
      `https://666e03bcd9ab9fc4.mokky.dev/favorites`
    );
    items.value = items.value.map((item) => {
      const favorite = favorites.find(
        (favorite) => favorite.item_id === item.id
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
  const localCart = localStorage.getItem("cart");
  cart.value = localCart ? JSON.parse(localCart) : [];

  await fetchItems();
  await fetchFavorites();
});

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false,
  }));
});

watch(filters, fetchItems);
</script>

<template>
  <div class="flex justify-between items-center flex-wrap">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
    <div class="flex items-center gap-5 flex-wrap">
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

  <div class="mt-10">
    <CardList
      :items="items"
      @add-to-favorite="addToFavorite"
      @add-to-cart="onClickAddPlus"
    />
  </div>
</template>
