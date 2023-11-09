<template>
  <v-app theme="light" >
    <div class="ma-10">
      <ColumnSelector
        :headers="headers"
        @select-headers-table="selectHeaders"
      ></ColumnSelector>
        <TableFilter
            :selectedHeaders="selectedHeaders"
            :headers="headers"
            :data="data"
        ></TableFilter>
    </div>
  </v-app>
</template>
<script setup>
    import { ref } from 'vue'
    import TableFilter from './components/TableFilter.vue';
    import ColumnSelector from './components/ColumnSelector.vue';
    import CP from '../data/places.json'

    const camelToFlat = ((camel) => {
      const camelCase =camel.replace(/([a-z])([A-Z])/g, '$1 $2').split(" ")

      let flat =""

      camelCase.forEach(word=>{    
          flat = flat + word.charAt(0).toUpperCase() + word.slice(1) + " "
      })  
      return flat
    })

    const selectedHeaders = ref([])

    const setWidth = ((item) => {
      if (typeof CP.pageItems[0][item] == 'number'){
        return `${item.length*1.5}rem`
      } 
      
      if (!CP.pageItems[0][item] || CP.pageItems[0][item] == 'none'){
        return `${item.length*1.1}rem`
      }

      return `${item.length*2.5}rem`
    })

    const setAlign = ((value)=>{
      if ( typeof value == 'number'){
        return 'end'
      }
      if (typeof value == 'boolean'){
        return 'center'
      }
      return 'start'
    })

    const headers = ref(Object.keys(CP.pageItems[0]).map((item, index) => {
      return {
        title: camelToFlat(item),
        key: item,
        type: typeof CP.pageItems[0][item],
        
        align: setAlign(CP.pageItems[0][item])
      }
    }))


    const data = ref(CP.pageItems)

    const selectHeaders = ((listOfColumns) => {
      selectedHeaders.value = headers.value.filter((item) => listOfColumns.value.includes(item.title))
    })

</script>