<template>
    <div>
    <v-text-field
            v-model="search"
            append-icon="mdi-magnify" 
            label="Search..."
        ></v-text-field>

        <v-data-table
            :headers="props.selectedHeaders"
            :items="filteredData"
            item-value="name"
            class="elevation-1"
            :search="search"
            multi-sort
            :loading="isLoading"
            loading-text="Loading..."
        >
        <template v-for="(header, index) in props.selectedHeaders" #[`column.${header.key}`]="{column}">
          {{ column.title }} 
          <FilterMenu
            :key="header.key"
            :filter-select="getValidSelectors(header.type)"
            :indexKey="header.key"
            @get-filter-values="getFilterValues"
          ></FilterMenu>
        </template>

        </v-data-table>
    </div>
</template>

<script setup>
    import { ref, onMounted, watch } from 'vue'
    import FilterMenu from './FilterMenu.vue';

    const props = defineProps(["headers", "selectedHeaders", "data"])

    const search = ref('')
    
    const isLoading = ref(false)
    
    
    const filteredData = ref([])
      
    onMounted(() => {
      filteredData.value = props.data;
    })

    const filterSelectorsText = ['contains', 'not contains', 'starts with', 'equals', 'not equals']
    const filterSelectorsNumeric = ['contains', 'not contains', 'greater than', 'less than', 'equals', 'not equals']

    const getValidSelectors = ((type) =>{
        if (type == 'text'){
            return filterSelectorsText
        } else if (type == 'numeric') {
            return filterSelectorsNumeric
        } else {
            // default
            return filterSelectorsText
        }
    })

    const filterValues = ref(new Array(props.headers.length))

    for (let i = 0; i < filterValues.value.length; i++) {
        filterValues.value[i] = {columnTitle: props.headers.at(i).key, filterOption: 'contains', filterValue: ''};
    }

    const getFilterValues = ((filterInfo) => {
        filterValues.value.map((item) => {
            if(item.columnTitle == filterInfo.key) {
                item.filterOption = filterInfo.filterOption;
                item.filterValue = filterInfo.filterVal;
            }
            return item;   
        });
    
        console.log(filterValues.value)
        filterDeserts();
    })

    const containsFilter = ((item, columnTitle, filterValue) => {
        return item[columnTitle].toString().toLowerCase().includes(filterValue.toLowerCase());
    })

    const notContainsFilter = ((item, columnTitle, filterValue) => {
        return !item[columnTitle].toString().toLowerCase().includes(filterValue.toLowerCase());
    })

    const startsWithFilter = ((item, columnTitle, filterValue) => {
        return item[columnTitle].toString().toLowerCase().startsWith(filterValue.toLowerCase());
    })

    const equalsFilter = ((item, columnTitle, filterValue) => {
        return item[columnTitle].toString().toLowerCase() == (filterValue.toLowerCase());
    })

    const notEqualsFilter = ((item, columnTitle, filterValue) => {
        return item[columnTitle].toString().toLowerCase() != (filterValue.toLowerCase());
    })

    const greaterThanFilter = ((item, columnTitle, filterValue) => {
        return Number(item[columnTitle]) > Number(filterValue);
    })

    const lessThanFilter = ((item, columnTitle, filterValue) => {
        return Number(item[columnTitle]) < Number(filterValue);
    })

    const filterDeserts = (() => {
      let conditions = [];

        filterValues.value.forEach((item) => {
            if (item.filterValue){
            
                switch(item.filterOption){
                    case 'contains': {
                        conditions.push({filterFunction: containsFilter, columnToFilter: item.columnTitle, filterValue: item.filterValue});
                        break;
                    }
                    case 'starts with': {
                        conditions.push({filterFunction: startsWithFilter, columnToFilter: item.columnTitle, filterValue: item.filterValue});
                        break;
                    }
                    case 'not contains': {
                        conditions.push({filterFunction: notContainsFilter, columnToFilter: item.columnTitle, filterValue: item.filterValue});
                        break;
                    }
                    case 'equals': {
                        conditions.push({filterFunction: equalsFilter, columnToFilter: item.columnTitle, filterValue: item.filterValue});
                        break;
                    }
                    case 'not equals': {
                        conditions.push({filterFunction: notEqualsFilter, columnToFilter: item.columnTitle, filterValue: item.filterValue});
                        break;
                    }
                    case 'greater than': {
                        conditions.push({filterFunction: greaterThanFilter, columnToFilter: item.columnTitle, filterValue: item.filterValue});
                        break;
                    }
                    case 'less than': {
                        conditions.push({filterFunction: lessThanFilter, columnToFilter: item.columnTitle, filterValue: item.filterValue});
                        break;
                    }
                    default:{
                        console.log('something wrong with the selection in filter section')
                    }
                }   
        }
      })

      if (conditions.length > 0) {
        filteredData.value = props.data.filter((item) => {
          return conditions.every((condition) => {
            return condition.filterFunction(item, condition.columnToFilter, condition.filterValue);
          })
        })
      }

      if (conditions.length == 0) {
        filteredData.value = props.data;
      }
    })
</script>