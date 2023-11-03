<template>
    <v-menu
        v-model="menu"
        :close-on-content-click="false"
        location="bottom"
    >
        <template v-slot:activator="{ props }">
        <v-icon small v-bind="props">
            mdi-filter
        </v-icon>
        </template>

        <v-card width="250">
        <v-card-title class="text-center">Filter</v-card-title>
        <v-divider></v-divider>
        <v-select
            v-model="optionFilterName"
            :items="props.filterSelect"
            value="contains"
            class="px-4 pt-4"
        ></v-select>
        

        <v-text-field
            v-model="filterValue"
            class="px-4 pt-1"
            type="text"
            label="Enter the search term"
            :autofocus="true"
        ></v-text-field>

        <v-card-actions class="ma-0">
            <v-spacer></v-spacer>

            <v-btn
            variant="text"
            @click="filterValue = ''"
            >
            Clean
            </v-btn>
            <v-btn
            color="primary"
            variant="text"
            @click="filterDeserts"
            >
            Apply
            </v-btn>
        </v-card-actions>
        </v-card>
    </v-menu>
    
</template>

<script setup>
    import { ref } from 'vue';

    const props = defineProps(["filterSelect", "data", "collumnTitle", "filteredData"]);
    const emits = defineEmits(["getFilteredData"])

    const menu =  ref(false);
    const optionFilterName = ref('contains');
    const filterValue = ref('');

    const containsFilter = ((item) => {
        return item[props.collumnTitle].toString().toLowerCase().includes(filterValue.value.toLowerCase());
    })

    const notContainsFilter = ((item) => {
        return !item[props.collumnTitle].toString().toLowerCase().includes(filterValue.value.toLowerCase());
    })

    const startsWithFilter = ((item) => {
        return item[props.collumnTitle].toString().toLowerCase().startsWith(filterValue.value.toLowerCase());
    })

    const equalsFilter = ((item) => {
        return item[props.collumnTitle].toString().toLowerCase() == (filterValue.value.toLowerCase());
    })

    const notEqualsFilter = ((item, col) => {
        return item[props.collumnTitle].toString().toLowerCase() != (filterValue.value.toLowerCase());
    })


    const filterDeserts = (() => {
      let conditions = [];

      if (filterValue.value) {
        switch(optionFilterName.value){
          case 'contains': {
            conditions.push(containsFilter);
            break;
          }
          case 'starts with': {
            conditions.push(startsWithFilter);
            break;
          }
          case 'not contains': {
            conditions.push(notContainsFilter);
            break;
          }
          case 'equals': {
            conditions.push(equalsFilter);
            break;
          }
          case 'not equals': {
            conditions.push(notEqualsFilter);
            break;
          }
          default:{
            console.log('something wrong with the selection in filter section')
          }
        }
        
      }

      if (conditions.length >= 0) {
        emits("getFilteredData", props.filteredData.filter((item) => {
          return conditions.every((condition) => {
            return condition(item);
          })
        }))
      }

      // if (conditions.length == 0) {
      //   emits("getFilteredData", props.data);
      // }

      menu.value = false;
    })

</script>