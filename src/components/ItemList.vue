<template>
    <div>
      <h1>Моя коллекция предметов</h1>
      <form @submit.prevent="addItem">
        <input v-model="newItem.name" placeholder="Название предмета" />
        <input v-model.number="newItem.quantity" type="number" placeholder="Количество" />
        <button type="submit">Добавить</button>
      </form>
  
      <label for="filter">Фильтр:</label>
      <input id="filter" v-model="filterQuery" placeholder="Фильтровать по имени" />
  
      <label for="sort">Сортировка:</label>
      <select id="sort" v-model="sortOrder">
        <option value="asc">По возрастанию</option>
        <option value="desc">По убыванию</option>
      </select>
  
      <ul>
        <li v-for="(item, index) in filteredAndSortedItems" :key="index">
          <div v-if="item.isEditing">
            <input v-model="item.name" placeholder="Название предмета" />
            <input v-model.number="item.quantity" type="number" placeholder="Количество" />
            <button @click="stopEditing(item)">Сохранить</button>
          </div>
          <div v-else>
            {{ item.name }} - {{ item.quantity }}
            <button @click="item.isEditing = true">Редактировать</button>
            <button @click="removeItem(index)">Удалить</button>
          </div>
        </li>
      </ul>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        items: [],
        newItem: {
          name: '',
          quantity: 0
        },
        filterQuery: '',
        sortOrder: 'asc'
      };
    },
    computed: {
      filteredAndSortedItems() {
        return this.items
          .filter(item => item.name.toLowerCase().includes(this.filterQuery.toLowerCase()))
          .sort((a, b) => {
            if (this.sortOrder === 'asc') {
              return a.name.localeCompare(b.name);
            } else {
              return b.name.localeCompare(a.name);
            }
          });
      }
    },
    mounted() {
      if (localStorage.getItem('items')) {
        this.items = JSON.parse(localStorage.getItem('items'));
      }
    },
    methods: {
      saveItems() {
        localStorage.setItem('items', JSON.stringify(this.items));
      },
      addItem() {
        this.items.push({ ...this.newItem, isEditing: false });
        this.newItem.name = '';
        this.newItem.quantity = 0;
        this.saveItems();
      },
      removeItem(index) {
        this.items.splice(index, 1);
        this.saveItems();
      },
      stopEditing(item) {
        item.isEditing = false;
        this.saveItems();
      }
    }
  };
  </script>