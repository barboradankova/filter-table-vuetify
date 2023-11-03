<template>
  <v-app theme="dark" >
    <div class="mx-10">
      
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify" 
        label="Search..."
        class="mt-10"
      ></v-text-field>

      
      <v-data-table
        :headers="headers"
        :items="filteredData"
        item-value="name"
        class="elevation-1"
        :search="search"
        multi-sort
        :loading="isLoading"
        loading-text="Loading..."
      >
        <template v-for="header in headers" #[`column.${header.key}`]="{column}">
          {{ column.title }}
          <FilterMenu 
          :filter-select="['contains', 'starts with', 'not contains', 'equals', 'not equals']"
          :filtered-data="filteredData"
          :data="data"
          :collumn-title="header.key"
          @get-filtered-data="changeData"
          :key="header.key"
          ></FilterMenu>
        </template>


      </v-data-table>
    </div>
  </v-app>
</template>
<script setup>
  import { ref, onMounted } from 'vue'
  import FilterMenu from './components/FilterMenu.vue';

    const search = ref('')
    
    const isLoading = ref(false)
    
    const headers = ref([
        {
          title: 'Dessert (100g serving)',
          align: 'start',
          sortable: false,
          key: 'name',
        },
        { title: 'Calories', align: 'end', key: 'calories' },
        { title: 'Fat (g)', align: 'end', key: 'fat' },
        { title: 'Carbs (g)', align: 'end', key: 'carbs' },
        { title: 'Protein (g)', align: 'end', key: 'protein' },
        { title: 'Iron (%)', align: 'end', key: 'iron' },
      ])
    const filteredData = ref([])
    const data = ref([
          {
            name: 'Frozen Yogurt',
            calories: 159,
            fat: 6.0,
            carbs: 24,
            protein: 4.0,
            iron: '1',
          },
          {
            name: 'Jelly bean',
            calories: 375,
            fat: 0.0,
            carbs: 94,
            protein: 0.0,
            iron: '0',
          },
          {
            name: 'KitKat',
            calories: 518,
            fat: 26.0,
            carbs: 65,
            protein: 7,
            iron: '6',
          },
          {
            name: 'Eclair',
            calories: 262,
            fat: 16.0,
            carbs: 23,
            protein: 6.0,
            iron: '7',
          },
          {
            name: 'Gingerbread',
            calories: 356,
            fat: 16.0,
            carbs: 49,
            protein: 3.9,
            iron: '16',
          },
          {
            name: 'Ice cream sandwich',
            calories: 237,
            fat: 9.0,
            carbs: 37,
            protein: 4.3,
            iron: '1',
          },
          {
            name: 'Lollipop',
            calories: 392,
            fat: 0.2,
            carbs: 98,
            protein: 0,
            iron: '2',
          },
          {
            name: 'Cupcake',
            calories: 305,
            fat: 3.7,
            carbs: 67,
            protein: 4.3,
            iron: '8',
          },
          {
            name: 'Honeycomb',
            calories: 408,
            fat: 3.2,
            carbs: 87,
            protein: 6.5,
            iron: '45',
          },
          {
            name: 'Donut',
            calories: 452,
            fat: 25.0,
            carbs: 51,
            protein: 4.9,
            iron: '22',
          },
      ])

    onMounted(() => {
      filteredData.value = data.value;
    })

    const changeData = ((newData) => {
        filteredData.value = newData;
    }

    )
    

</script>