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
        <template v-for="(header, index) in props.selectedHeaders" #[`column.${header.key}`]="{ column }">
          {{ column.title }} 
          <FilterMenu
            :key="header.key"
            :filter-select="getValidSelectors(header.type)"
            :indexKey="header.key"
            @get-filter-values="getFilterValues"
          ></FilterMenu>
        </template>

        <template v-for="header in props.selectedHeaders" #[`item.${header.key}`]="{ item }">
            <div v-if="header.key != 'note' && typeof item[header.key] != 'boolean'" :key="header.key">{{ item[header.key] }}</div>
            <v-chip v-if="header.key != 'note' && typeof item[header.key] == 'boolean'" :key="header.key" :color="`${item[header.key] == true ? 'green-darken-2': 'red-darken-1'}`">
                {{ `${item[header.key] == true ? 'yes': 'no'}`}}
            </v-chip>
            <PopupNote v-if="header.key == 'note'" :key="header.key" :note="item[header.key]"></PopupNote>          
        </template>

        </v-data-table>
    </div>
</template>

<script setup>
    import { ref, onMounted, watch } from 'vue'
    import FilterMenu from './FilterMenu.vue';
    import PopupNote from './PopupNote.vue';

    const props = defineProps(["headers", "selectedHeaders", "data"])

    const search = ref('')
    
    const isLoading = ref(false)

    const dialog = ref(false)
    
    
    const filteredData = ref([])
      
    onMounted(() => {
      filteredData.value = props.data;
    })

    const filterSelectorsText = ['contains', 'not contains', 'starts with', 'equals', 'not equals']
    const filterSelectorsNumeric = ['contains', 'not contains', 'greater than', 'less than', 'equals', 'not equals']
    const filterSelectorsBoolean = ['yes', 'no']

    const getValidSelectors = ((type) =>{
        if (type == 'number'){
            return filterSelectorsNumeric
        }
        if (type == 'boolean') {
            return filterSelectorsBoolean
        }
        // default
        return filterSelectorsText
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
                    case 'yes' : {
                        conditions.push({filterFunction: equalsFilter, columnToFilter: item.columnTitle, filterValue: 'true'});
                        break;
                    }
                    case 'no': {
                        conditions.push({filterFunction: equalsFilter, columnToFilter: item.columnTitle, filterValue: 'false'});
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