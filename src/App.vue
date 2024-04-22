<script setup>
import { onMounted, ref, watch, reactive } from "vue";
import axios from "axios";
import TheHeader from "./components/TheHeader.vue";
import CardList from "./components/CardList.vue";
import TheDrawer from "./components/TheDrawer.vue";

const items = ref([]); // state хранящий все наши кроссовки. ref для хранения массива

// реактивный state хранящий наши фильтры. reactive для хранения объекта
const filters = reactive({
  sortBy: "title",
  searchQuery: "",
});

// функции следящие за изменением селекта и наших фильтров
const onChangeSelect = (event) => {
  filters.sortBy = event.target.value;
};

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value;
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

    items.value = data;
  } catch (err) {
    console.log(err);
  }
};

onMounted(fetchItems);

watch(filters, fetchItems);
</script>

<template>
  <!-- <TheDrawer /> -->
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <TheHeader />

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

      <CardList :items="items" />
    </div>
  </div>
</template>
